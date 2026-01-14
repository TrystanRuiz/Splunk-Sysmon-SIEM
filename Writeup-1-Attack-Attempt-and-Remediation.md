PART 1 ATTACK and INVESTIGATION
Ok this first image below simulates an attempt of an attacker on Kali trying to connect to the target and potentially access files
<p align="center">
  <img src="pictures/attackattemptkali.png" width="600" height="350">
</p>



In the picture below on the dashboard in the top right you can see the Failed Logons section (Event 4625) 
This notifies me of an attempted logon with user credentials
At the bottom and in the next picture it goes more into depth on the attack and gives you items needed to remediate
<p align="center">
  <img src="pictures/dash1.png" width="600" height="350">
</p>




The picture blow isolates the malicious Ip address 192.168.1.75
It also lists the name of the workstation used to attack "Kali" 
In the previous picture it showed 15 failed logon attempts and every attempt looks like this 
<p align="center">
  <img src="pictures/attackattempt.png" width="600" height="350">
</p>

PART 2 REMEDIATION
