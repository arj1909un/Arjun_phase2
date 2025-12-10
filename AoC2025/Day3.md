# Log analysis with spunk

## Solution:
```
Search query: index=main
```
- this query all time gives us all the ingested logs

```
Search query: index=main sourcetype=web_traffic | timechart span=1d count
```
- this query gives us traffic on web of total logs for each day and there was a spike for 6 specfific days.
- then we go to the user agent field which tells us what software initiated the web request

```
Search query: index=main sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
```
- searches for traffic on non standard browser and all of them are from the same ip address 

```
Search query: sourcetype=web_traffic client_ip="<REDACTED>" AND path="*..\/..\/*" OR path="*redirect*" | stats count by path
```
- this tells us what path the hacker used which checks for vulnerabilities so gain access of unauthorized files on the website

```
Search query: sourcetype=web_traffic client_ip="<REDACTED>" AND user_agent IN ("*sqlmap*", "*Havij*") | table _time, path, status
```
- the results confirms the use of known SQL injection and attacks like SLEEP. A 504 code confirms a successful time-based SQL injection attack. 

```
Search query: sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED" | table _time, action, protocol, src_ip, dest_ip, dest_port, reason
```
- now we switch to firewall and use our comprimised ip and the output gives us allowed which confirms succesful connection

```
Search Query: sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED" | stats sum(bytes_transferred) by src_ip
```
- This gives the number of bytes transfered
  
## Flag:
```
- 198.51.100.55 (1)*
- 2025-10-12 (2)*
- 993 (3)*
- 658 (4)*
- 126167 (5)8
```
