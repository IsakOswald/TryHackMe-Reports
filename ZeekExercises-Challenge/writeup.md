The room invites you a challenge to investigate a series of traffic data and stop malicious activity under different scenarios. Let's start working with Zeek to analyse the captured traffic.

### Section 1

**An alert triggered:** "Anomalous DNS Activity".

The case was assigned to you. Inspect the PCAP and retrieve the artefacts to confirm this alert is a true positive.



![Q1](/images/Q1.png)

First let's head to the directory where the task files are.

![S1](/images/S1.png)

Now, lets go ahead and use zeek by running `zeek -Cr dns-tunneling.pcap` to generate out log files from the .pcap file.

![S2](/images/S2.png)

As you can see, we now have our different types of logs. Zeek is great for network forensics as it has many different log categories to avoid having to sift through just one big log file. 

Let's continue by printing out the dns.log file as per the question. We can use a simple cat for this.

![S3](/images/S3.png)

As you can see, we have some different DNS records (MX, CNAME, TXT). Since the question is asking specifically for IPv6 records, lets grep by "AAAA" and take the line count. I used the command `grep “AAAA” dns.log | wc -l`.

![S4](/images/S4.png)

And as we can see, 320 was the answer to the first question.



![Q2](/images/Q2.png)

Now we need to investigate the conn.log file. I used `less conn.log` to find out the fields to then filter using `zeek-cut`. 

![S5](/images/S5.png)

As denoted by the "#fields" there are different things we can filter with `zeek-cut`. The filter we are looking for is "service duration".

Now let’s go ahead and use the command `cat conn.log | zeek-cut service duration | sort` so we can easily see the longest duration.

![S6](/images/S6.png)

As we can see, a http service had the longest connection duration.



![Q3](/images/Q3.png)

This was probably the trickiest question in the challenge. Let’s start off by opening up the log once again with `less` to see the fields.

![S7](/images/S7.png)

As we can see, there is a "query" field which is what we are looking for. Lets use the command `cat dns.log | zeek-cut query |rev | cut -d '.' -f 1-2 | rev | sort |uniq | wc -l` as there are many duplicated patterns having "*** .cisco-update.com"

![S8](/images/S8.png)

As you can see, we have our answer of 6.



![Q4](/images/Q4.png)

We know that the massive amount of queries were being sent to cisco-update(.)com. Let's grep that pattern out and see if we can find an IP. I used `I used grep “dns” conn.log`.

![S9](/images/S9.png)

We have this same IP making these dns requests.

### Section 2



![Q5](/images/Q5.png)

Let's first head to the directory and print some logs.

![S10](/images/S10.png)

Now let's cat out conn.log with `cat conn.log`.

![S11](/images/S11.png)

As you can see, 10.6.27.102 seems to be making many connections. This must be what the question is looking for.



![Q6](/images/Q6.png)

Let's analyse the http.log file with `cat http.log`.

![S12](/images/S12.png)

Luckily, there are not many logs here and we can see that some knr.exe is being downloaded from smart-fax(.)com. Keep in mind the .exe as it will come up in a future question.



![Q7](/images/Q7.png)

First we must find out the hash so we can search in VirusTotal. I use the zeek script provided with the command `zeek -Cr phishing.pcap hash-demo.zeek`.

![S13](/images/S13.png)

After printing out the files.log with `zeek-cut`, there are sadly no file names, but if we look back to the output of http.log we can see that the /msword is the hash to initially look for.

![S14](/images/S14.png)

As we can see, this is a VBA script.



![Q8](/images/Q8.png)

By inspecting the other hash we see the following in VirusTotal:

![S15](/images/S15.png)

As we can see, **PleaseWaitWindow** is what we are looking for.



![Q9](/images/Q9.png)

Under the Behaviour tab, we can see its DNS resolutions which is where we find our answer.

![S16](/images/S16.png)



![Q10](/images/Q10.png)

As we already identified in our analysis with zeek, the name is kvxr (I told you to remember this). If you cant remember where I got this from, have a look at the previous analysis of the **http.log**.

### Section 3



![Q11](/images/Q11.png)

Let’s first start by moving to the log4j directory and running zeek with the scrip provided.

![S17](/images/S17.png)

Now we have our logs, let’s cat the signatures.log file, and as we can see, there has been 3 hits.

![S18](/images/S18.png)



![Q12](/images/Q12.png)

Let's cat out the http.log file

![S19](/images/S19.png)

As we can see in the user agent info, it says something about the nmap scripting engine.



![Q13](/images/Q13.png)

Let's cat out the http.log file one more time with `less http.log`.

![S20](/images/S20.png)

As we can see, it’s some file with the .class extension called "Exploit".



![Q14](/images/Q14.png)

Let's start off by catting out the log4j.log file. I use less here.

![S21](/images/S22.png)

As seen above, there is certainly some base64 encoding going on. Let’s take this to **Cyber Chef** to attempt to decode it.

![S22](/images/S22.png)

Great job on completing the room. I hope you learned something and thank you for reading.