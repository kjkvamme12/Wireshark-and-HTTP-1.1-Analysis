# Wireshark and HTTP/1.1 Analysis

## Objective
- Look at HTTP transactions in Wireshark
- Gain situational awareness when looking at PCAP of unknown contents.
- Analyze headers of HTTP/1.1 transaction
- Follow an HTTP session to see details of a converation.
- Carve files out of HTTP packet capture.
- Find evidence of compromise via HTTP packet capture. 

### Skills Learned
[Bullet Points - Remove this afterwards]

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
 - Wireshark
   
## Steps

### Protocol Hierchy
1. The protocol hierchy option in Wireshark
     - Statistics > Protoco Hierchy
  ![Screenshot 2025-01-30 140008](https://github.com/user-attachments/assets/719988a4-a2a5-474f-8f7d-90f5096b0933)

This will bring a pop up andshow contents of the packet capture, summarized by oSI layers starting from the frames and going all the way down into application layer protocols. 
Looking at this, it appears that the packet capture is all TCP packets, and nearly all are hTTP protocol (a few WebSockets as well). 
Looking deeper into the HTTP traffic, we see some of the files transferred. Even Domain Name System is listed because this contains DNS over HTTPS. This appears to contain nearly all standard HTTP and some DoH


### Conversations List 

To view conversation lists, go to Statistics > Conversation 
1. In this pop up , click on the TCP tab which will show each connection from a source IP/port to destination IP/Port perspective




![Screenshot 2025-01-30 140932](https://github.com/user-attachments/assets/5626bcb2-996f-4cc4-aa8c-bacb1a50e2b5)

Usually if hunting for malicious activity, it is good to sort by port as odd ports are often used for malicious downloads and command and control traffic. 

This list only shows port 80, which is normal given that in our previous protocol hiercy, all the traffic is HTTP. There is some traffic to different IP addresses which may need to be inspected. 

### HTTP/1.1 Analysis
 ### 1. Apply the http display filter in Wireshark since we know the capture is all http.


### 2. Show the Host Column

   - The host column is very useful to show when analyzing HTTP
   - once http filter is applied, click first line, then in middle of the page go to packet details, and expand the line that says Hyptertext Transfer Protocol, then click Host: header
  
  
![Screenshot 2025-01-30 141855](https://github.com/user-attachments/assets/b646b8de-ef45-4f16-aa2c-663d36fa9e7b)


![Screenshot 2025-01-30 141936](https://github.com/user-attachments/assets/2fb26fa5-2a19-4e19-aa88-ee26667e1c31)

Having a host column makes IP-based filtering easier, because now we can associate an IP with a domain name


### 2. Viewing HTTP/1.1 Headers in Packet Details

Pull packet 43 into focus. 

![Screenshot 2025-01-30 143609](https://github.com/user-attachments/assets/ba36b511-a4c3-4419-9fe7-0490a88fa90b)

We see here that the user was browsing to the BBC website. This packet would be the initial GET request used to request the root of the webpage. 
 - The HTTP method, which is GET
 - The URL that is being loaded /
 - The version of HTTP being used HTTP/1.1

Now go to packet details and expand the Hypter Text Transfer Protocol


![Screenshot 2025-01-30 143844](https://github.com/user-attachments/assets/4aa9ad69-958d-4ab0-8eef-d16fc365d1db)

All the headers from the HTTP request are shown here. 
Now go inspect packet headers from packet 89, which appears to be the response to 43. 


![Screenshot 2025-01-30 144049](https://github.com/user-attachments/assets/ce0e3ac6-6876-4482-8583-3e9fc1cd6726)

### 3. Following an HTTP/1.1 Session 

To follow a session, right-click then seleect Follow > HTTP Stream

![Screenshot 2025-01-30 144301](https://github.com/user-attachments/assets/5ded5561-6f24-4154-bfc5-aff1fa598921)

This gives us the full conversation with the request shown in red, and the response in blue. 


### 4. Extracting Files from an HTTP/1.1 Packet Capture

To extract files from this Wireshark capture, File > Export Objects > HTTP


![Screenshot 2025-01-30 150339](https://github.com/user-attachments/assets/64e99f1a-a9d4-4464-841d-ba4c68a05ad2)

This brings us to a window with all the HTTP trnsactions listed, as well as associated Hostnames, Content Types, Sizes, and Filenames. 

Once we search what we are looking for, we can view the file in our desktop. This is how we carve a file out of an hTTP packet capture

## Additional Question
Take a look at the packet capture, do you notice anything suspicious?

1. After scrolling through and observing the hosts, the account-infos-paypal.com   does look suspicious.
   
![Screenshot 2025-01-30 152641](https://github.com/user-attachments/assets/c78a409e-be7c-4675-af34-2e92fcb13674)

2. Now filter down traffic just to this domain name.
  - Locate the IP address and add as a filter. 
