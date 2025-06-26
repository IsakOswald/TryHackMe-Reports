# Brim Challenge - Masterminds

## Learning Room: https://tryhackme.com/room/brim
You should note that this is **NOT** a tutorial on Brim, but a walkthrough of the **challenge** room. If you need to lead what Brim is and how to use it, refer to the **Learning Room** link.

## Challenge Room: https://tryhackme.com/room/mastermindsxlq


The goal of this challenge is to practise analysing malicious traffic using Brim.

### TASK 1:

Task one involved setting up and deploying the VM. I will skip this and get right into actually using Brim.

### TASK 2:

#### Q1:

Let’s start off by loading the **infection1** packet capture (.pacp) into Brim.

![S1](images/S1.png)

We will then continue by then looking at the “conn” logs to try and specify out victims IP address. I used the search query `_path == “conn” | cut id.orig_h,id.resp_h` and got the following:


![S2](images/S2.png)

As we can see, there seems to be a lot of outgoing connections from **192.168.75.249** which is the answer to the first question.

![Q1](images/Q1.png)

#### Q2:

I use the search query `_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method,host, uri, status_code | uniq -c` to search for the unique http connections and their **status codes**.

![S3](images/S3.png)

As we can see on the left hand side, we see two HTTP requests resulting in a **404** not found. We can then look in the host field (field just means column) to find our two domains.

![Q2](images/Q2.png)

#### Q3:

I use the query `_path=="http" and response_body_len == 1309 | cut id.resp_h,host` to search for the http logs which have a response body length of 1309 (as specified by the question)

![S4](images/S4.png)

As we can see the **id.resp_h** field (which is just destination IP) we have what seems to the attackers IP. We can also see the domain name next to it.

![Q3](images/Q3.png)

#### Q4

I first filter by the unique DNS Queries with` _path=="dns" | count() by query | sort -r` and get the following output:

![S5](images/S5.png)

The first and last query are pretty much duplicates however with different capitalisation. 6 + 1 = 7 total queries.

![Q4](images/Q4.png)

#### Q5

I decide to filter by the host here. I used the query `_path=="http" and host == "bhaktivrind[.]com”` (note this is defanged here but not in the actual query).

![S6](images/S6.png)

As we can see from the output, we have the answer under the "uri" field.

![Q5](images/Q5.png)

#### Q6:

First let's query for the unique http requests with `_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method,host, uri | uniq -c`. We see the following response:

![S7](images/S7.png)

As we can see, there is a suspicious **catzx.exe** comming from the IP ending in 112 (id.resp_h).

![Q6](images/Q6.png)

#### Q7:

From the second question (Q2), we found the **cambiasuhistoria(.)growlab(.)es** domain. Let’s search for it in Virus Total

![S8](images/S8.png)

As we can see under the community tab, we have our answer "Emotet".

![Q7](images/Q7.png)

### TASK 3:

### Q1:

After loading up the “Infection2.pcap” file I search for the unique network connections using the query `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq` and get the following:

![S9](images/S9.png)

As we can see there are a-lot of established connection involving 192.168.75.146, let's try that as the answer.

![Q8](images/Q8.png)

### Q2:

I use the query `method="POST | cut ts, uid, id, method, uri, status_code` to find the unique POST requests made.

![S10](images/S10.png)

As seen under the **id.resp_p** field, we can see the IP the victim has been making POST requests to.

![Q9](images/Q9.png)

#### Q3:

Using the same query we can see how many times the request was made by the victim.

![S10](images/S10.png)

![Q10](images/Q10.png)

#### Q4:

Let's query for the http requests made with `_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method,host, uri | uniq -c`. We get the following result:

![S11](images/S11.png)

As we can see in the **host** field, there was a suspicious request to a domain downloading a .exe file.

![Q11](images/Q11.png)

#### Q5:

Using the same query we can also identify the name and full URI of the binary (exe file).

![S11](images/S11.png)


![Q12](images/Q12.png)

#### Q6:

Again using the same query, we are able to find the IP under the **id.resp_h** field.

![S11](images/S11.png)

![Q13](images/Q13.png)

#### Q7:

I view the alerts with the query `event_type=="alert" alert.severity==1 alert.category=="A Network Trojan was detected"` and get:


![S12](images/S12.png)

As we can see in the response, there were two requests made from the same host.

![Q14](images/Q14.png)

#### Q8:

After heading to **URLhaus** and pasting in the domain I get the following results:

![S13](images/S13.png)

As seen in the tags section, it seems to be named "RedLineStealer".

![Q15](images/Q15.png)

### TASK 4:

#### Q1:

I use the same query to find the unique network connections `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq`.

![S14](images/S14.png)

Looking at the **id.orig_h** field, we can see many established connections involving the IP ending in .232

![Q16](images/Q16.png)

#### Q2:

I use the same query we have been using to find the unique network connections `_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq` and get:

![S15](images/S15.png)

We can see there are many numbered **.exe** files being downloaded from very similar looking domain that are just starting with a different letter.

![Q17](images/Q17.png)

#### Q3:

Using the same query, we can also identify the three different IPs by looking in the **id.resp_h** field.

![S15](images/S15.png)

![Q18](images/Q18.png)

#### Q4:

I first query for unique domains and then search for our efhoahegue[.]ru with `_path=="dns" | count() by query | sort -r | efhoahegue[.]ru`

![S16](images/S16.png)

As we can see, there were two queries made to resolve this domain.

![Q19](images/Q19.png)

#### Q5:

Let’s use the query `_path=="http" | cut id.orig_h, id.resp_h, id.resp_p, method,host, uri | uniq -c` once more to find the different binaries being downloaded.

![S15](images/S15.png)

As we can see it's 1-5.exe meaning 5 different binaries are being downlaoded.

![Q20](images/Q20.png)

#### Q6:

I use the query `_path=="http" | cut host,user_agent | sort`  to easily see the user agent in the output.

![S17](images/S17.png)

As seen in the user_agent field, it seems to be FireFox.

![Q21](images/Q21.png)

#### Q7:

I use the following query to find the total DNS queries`_path=="dns" | count()` and get the output:

![S18](images/S18.png)

We get 986 DNS connections as our result.

![Q22](images/Q22.png)

#### Q8:

I decide to paste the domain into **VirusTotal** and have a look in the community section. You could have just searched for “efhoahegue worm” in google and found the same result. Here is what I found in VirusTotal:

![S19](images/S19.png)

Down the very bottom we can see “phorpiex” which is close (but missing a letter). I then did a google search and found the missing ‘h’.

![Q23](images/Q23.png)

I hope you leaned a bit about Brim and thank you for reading.