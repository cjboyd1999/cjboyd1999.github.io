---
title: "Python Project: SYN Scanner"
date: 2020-03-31
tags: [Cyber Security]
excerpt: "Python, SYN Scanner, Cyber Security"
---

## Project Scope
This project was completed as part of my university course SIT379 - Ethical Hacking. In which, I was tasked with creating a Python script that utilises the Scapy package to conduct a SYN scan on a range of ports.

## The Script
As is referenced within the script, I utilised an existing SYN scanner by mic159 (2017) which can be found [here](https://gist.github.com/mic159/c7133509af81dad409b79b8c4838f4bd).

```python
'''
REFERENCES
mic159 2017, 'SYN scan in python', GitHub Gist, retrieved 31 March 2020, <https://gist.github.com/mic159/c7133509af81dad409b79b8c4838f4bd>
'''

# Importing the necessary libraries
import time
import logging
from scapy.all import *

# Create an empty list and set the target IP address
open_ports = []
ip = '192.168.1.103'

# A function to test if the host is up
def is_up(ip):
	icmp = IP(dst=ip)/ICMP()
	resp = sr1(icmp, timeout=10)
	if resp == None:
		return False
	else:
		return True

# Code executed at runtime
if __name__ == '__main__':

	# Stop the console from listing packets
	conf.verb = 0

	# Get and store the start time of the scan
	start_time = time.time()

	# Set a range of ports to scan
	ports = range(1, 1024)

	# Check if the host is up
	if is_up(ip):

		# Print the target ip
		print("Host " + ip + " is up. Starting scan:")

		# For each port in between the range (1 - 1024), create a SYN packet and send it
		for port in ports:
			packet = IP(dst=ip)/TCP(dport=port, flags='S')
			answers, un_answered = sr(packet, timeout=0.2)

			'''
			For each request and response in answers, if the response doesn't have a TCP layer, ignore the following:
				- Check if there are SYN+ACK packets
				- If so, save the port to the list of open ports
				- Reset the connection with the port as to not leave half-open connections
			'''
			for req, resp in answers:
				if not resp.haslayer(TCP):
					continue				
				tcp_layer = resp.getlayer(TCP)
				if tcp_layer.flags == 0x12:
					open_ports.append(tcp_layer.sport)
					sr(IP(dst=ip)/TCP(dport=port, flags='AR'), timeout=1)

		# Print the list of open ports		
		print("OPEN PORTS:")		
		for p in open_ports:
			print(p)		

		# Calculate the total scan time and print it along with the target IP
		duration = time.time() - start_time
		print("Scan of " + str(ip) + " completed in " + str(duration) + " seconds.")

	#If the host is not up, state that it is down
	else:
		print("Host " + ip + " is down.")

```

## Project Outcome
As this was only one of many tasks towards a portfolio, it did not have a direct influence on my grade at the time of completion. However, it was a High Distinction task that had to be completed in order to achieve the High Distinction (maximum) grade.
