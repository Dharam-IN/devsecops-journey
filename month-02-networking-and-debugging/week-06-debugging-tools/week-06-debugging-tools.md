# tcpdump basics
tcpdump is a tool that watched (capture) all the network packets coming and going out of your system.

# data - how tcpdump get data
tcpdump is just show the live packets us by default with this command:-
```
sudo tcpdump -i any
```
Output comes something like this:-

```
ubuntu@ip-172-31-44-194:~$ sudo tcpdump -i any
tcpdump: WARNING: any: That device doesn't support promiscuous mode
(Promiscuous mode not supported on the "any" device)
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
12:13:44.026162 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 1383527623:1383527787, ack 4085729077, win 464, options [nop,nop,TS val 484689912 ecr 1847987450], length 164
12:13:44.029372 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 164:368, ack 1, win 464, options [nop,nop,TS val 484689916 ecr 1847987450], length 204
12:13:44.124798 lo    In  IP localhost.35246 > _localdnsstub.domain: 51744+ [1au] PTR? 123.68.208.103.in-addr.arpa. (56)
12:13:44.125030 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.41658 > ip-172-31-0-2.eu-north-1.compute.internal.domain: 2732+ [1au] PTR? 123.68.208.103.in-addr.arpa. (56)
12:13:44.130312 ens5  In  IP ip-172-31-0-2.eu-north-1.compute.internal.domain > ip-172-31-44-194.eu-north-1.compute.internal.41658: 2732 NXDomain 0/1/1 (144)
12:13:44.130569 lo    In  IP _localdnsstub.domain > localhost.35246: 51744 NXDomain 0/1/1 (144)
12:13:44.130727 lo    In  IP localhost.40590 > _localdnsstub.domain: 44812+ [1au] PTR? 194.44.31.172.in-addr.arpa. (55)
12:13:44.130881 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.33284 > ip-172-31-0-2.eu-north-1.compute.internal.domain: 4969+ [1au] PTR? 194.44.31.172.in-addr.arpa. (55)
12:13:44.131746 ens5  In  IP ip-172-31-0-2.eu-north-1.compute.internal.domain > ip-172-31-44-194.eu-north-1.compute.internal.33284: 4969 1/0/1 PTR ip-172-31-44-194.eu-north-1.compute.internal. (113)
12:13:44.131856 lo    In  IP _localdnsstub.domain > localhost.40590: 44812 1/0/1 PTR ip-172-31-44-194.eu-north-1.compute.internal. (113)
12:13:44.132093 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 368:844, ack 1, win 464, options [nop,nop,TS val 484690018 ecr 1847987450], length 476
12:13:44.160182 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 4294966984, win 471, options [nop,nop,TS val 1847987665 ecr 484689831], length 0
12:13:44.160183 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 4294967028, win 471, options [nop,nop,TS val 1847987665 ecr 484689831], length 0
12:13:44.164151 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 0, win 469, options [nop,nop,TS val 1847987671 ecr 484689837], length 0
12:13:44.224085 lo    In  IP localhost.59694 > _localdnsstub.domain: 31567+ [1au] PTR? 53.0.0.127.in-addr.arpa. (52)
12:13:44.224341 lo    In  IP _localdnsstub.domain > localhost.59694: 31567*$ 1/0/1 PTR _localdnsstub. (79)
12:13:44.226937 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 844:1000, ack 1, win 464, options [nop,nop,TS val 484690113 ecr 1847987671], length 156
12:13:44.226963 lo    In  IP localhost.47710 > _localdnsstub.domain: 41199+ [1au] PTR? 2.0.31.172.in-addr.arpa. (52)
12:13:44.227149 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.39455 > ip-172-31-0-2.eu-north-1.compute.internal.domain: 14990+ [1au] PTR? 2.0.31.172.in-addr.arpa. (52)
12:13:44.227765 ens5  In  IP ip-172-31-0-2.eu-north-1.compute.internal.domain > ip-172-31-44-194.eu-north-1.compute.internal.39455: 14990 1/0/1 PTR ip-172-31-0-2.eu-north-1.compute.internal. (107)
12:13:44.227880 lo    In  IP _localdnsstub.domain > localhost.47710: 41199 1/0/1 PTR ip-172-31-0-2.eu-north-1.compute.internal. (107)
12:13:44.228309 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [.], seq 1000:2400, ack 1, win 464, options [nop,nop,TS val 484690115 ecr 1847987671], length 1400
12:13:44.228312 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 2400:2916, ack 1, win 464, options [nop,nop,TS val 484690115 ecr 1847987671], length 516
12:13:44.240066 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 164, win 468, options [nop,nop,TS val 1847987746 ecr 484689912], length 0
12:13:44.244057 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 368, win 467, options [nop,nop,TS val 1847987749 ecr 484689916], length 0
12:13:44.324339 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 2916:3880, ack 1, win 464, options [nop,nop,TS val 484690211 ecr 1847987749], length 964
12:13:44.324382 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 3880:4888, ack 1, win 464, options [nop,nop,TS val 484690211 ecr 1847987749], length 1008
12:13:44.364531 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 844, win 464, options [nop,nop,TS val 1847987873 ecr 484690018], length 0
12:13:44.424261 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 4888:5532, ack 1, win 464, options [nop,nop,TS val 484690311 ecr 1847987873], length 644
12:13:44.440682 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 1000, win 463, options [nop,nop,TS val 1847987947 ecr 484690113], length 0
12:13:44.440682 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 2916, win 449, options [nop,nop,TS val 1847987948 ecr 484690115], length 0
12:13:44.524250 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 5532:6160, ack 1, win 464, options [nop,nop,TS val 484690411 ecr 1847987948], length 628
12:13:44.540844 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 3880, win 442, options [nop,nop,TS val 1847988045 ecr 484690211], length 0
12:13:44.540844 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 4888, win 435, options [nop,nop,TS val 1847988045 ecr 484690211], length 0
12:13:44.624237 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 6160:6788, ack 1, win 464, options [nop,nop,TS val 484690511 ecr 1847988045], length 628
12:13:44.637812 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 5532, win 430, options [nop,nop,TS val 1847988145 ecr 484690311], length 0
12:13:44.724164 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 6788:7224, ack 1, win 464, options [nop,nop,TS val 484690610 ecr 1847988145], length 436
12:13:44.738141 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 6160, win 426, options [nop,nop,TS val 1847988244 ecr 484690411], length 0
12:13:44.824136 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 7224:7660, ack 1, win 464, options [nop,nop,TS val 484690710 ecr 1847988244], length 436
12:13:44.837838 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 6788, win 422, options [nop,nop,TS val 1847988345 ecr 484690511], length 0
12:13:44.924217 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 7660:8096, ack 1, win 464, options [nop,nop,TS val 484690811 ecr 1847988345], length 436
12:13:44.938482 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 7224, win 419, options [nop,nop,TS val 1847988444 ecr 484690610], length 0
12:13:45.024273 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 8096:8532, ack 1, win 464, options [nop,nop,TS val 484690911 ecr 1847988444], length 436
12:13:45.039252 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 7660, win 416, options [nop,nop,TS val 1847988545 ecr 484690710], length 0
12:13:45.124291 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 8532:8968, ack 1, win 464, options [nop,nop,TS val 484691011 ecr 1847988545], length 436
12:13:45.138882 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 8096, win 413, options [nop,nop,TS val 1847988644 ecr 484690811], length 0
12:13:45.224248 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 8968:9404, ack 1, win 464, options [nop,nop,TS val 484691111 ecr 1847988644], length 436
12:13:45.238802 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 8532, win 410, options [nop,nop,TS val 1847988744 ecr 484690911], length 0
12:13:45.324206 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 9404:9840, ack 1, win 464, options [nop,nop,TS val 484691210 ecr 1847988744], length 436
12:13:45.338836 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 8968, win 407, options [nop,nop,TS val 1847988844 ecr 484691011], length 0
12:13:45.424261 ens5  Out IP ip-172-31-44-194.eu-north-1.compute.internal.ssh > 103.208.68.123.45898: Flags [P.], seq 9840:10276, ack 1, win 464, options [nop,nop,TS val 484691311 ecr 1847988844], length 436
12:13:45.442935 ens5  In  IP 103.208.68.123.45898 > ip-172-31-44-194.eu-north-1.compute.internal.ssh: Flags [.], ack 9404, win 404, options [nop,nop,TS val 1847988945 ecr 484691111], length 0
^C
52 packets captured
63 packets received by filter
0 packets dropped by kernel
ubuntu@ip-172-31-44-194:~$ 

```



this is live data that show on the screen but if we want to save this data to any file we can use this command.

```
sudo tcpdump -i any -w mycapture.pcap
```

for read the data.

```
tcpdump -r mycapture.pcap
```


