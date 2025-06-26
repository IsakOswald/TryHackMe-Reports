### What is Zeek?

Zeek is not a IDS/IPS like snort is, it's more of a log generator or forensic analyst. Zeek analyses .pcap files and splits them up into many different types of logs so you can easily search for http/connection/signature logs without having to sift through one massive log file. Zeek CAN be used as an IDS with *zeekctl* however not often as there are better tools for the job. If Zeek is used as a IDS, it detects packets based on signatures specified in the zeek.sig. Zeek also has its own scripting language for rapid prototyping making it a great rule. It also has frameworks (like downloadable scripts) to for example find the file hashes in the files.log and output them to you. Zeek logs have something called "fields" which are pretty much columns which make them very structured (you use Zeek-Cut on these fields). 

If this sounds like nonsense check out:

https://tryhackme.com/room/zeekbro