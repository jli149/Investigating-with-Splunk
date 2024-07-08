# Investigating-with-Splunk
Investigate anomalies using Splunk.

Link to exercise: https://tryhackme.com/r/room/investigatingwithsplunk

SOC Analyst Johny has observed some anomalous behaviors in the logs of a few Windows machines. It looks like the adversary has access to some of these machines and successfully created some backdoors. His manager has asked him to pull those logs from suspected hosts and ingest them into Splunk for a quick investigation. Our task as SOC Analysts is to examine the logs and identify the anomalies.

	1. How many events were collected and Ingested in the index main?
	
	Query: index = main
  Answer: 12,256
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/5e620ffa-b521-42bd-8cf4-486eb48a5d95)
  

	2. On one of the infected hosts, the adversary was successful in creating a backdoor user. What is the new username?
	
	EvernID 4720 stands for a user account was created.
	Query: index=main EventID=4720
  Answer: A1berto

  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/2186cdfd-6ad1-4b01-aff0-4212207fabb0)
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/4220e6f8-ff5f-49f8-b92f-e51eb687e31f)
  	
  
	3. On the same host, a registry key was also updated regarding the new backdoor user. What is the full path of that registry key?
 
	Since a registry key was updated, we use EventID=13 to filter out the event. 
	Query: index=main A1berto EventID=13
  Answer: HKLM\SAM\SAM\Domains\Account\Users\Names\A1berto
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/ff9c24f2-c169-46fc-808b-d6da87818310)
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/b2be5dff-820e-4dd4-a13d-24e2c073f8a5)



	4. Examine the logs and identify the user that the adversary was trying to impersonate.
 
  Answer: Alberto
 
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/02339d0e-15b3-4ccf-b069-b5520e23e920)

	5. What is the command used to add a backdoor user from a remote computer?

	We use EventID 4688 to filter out all command line input whenever a new process has been created. 
	Query: index=main EventID=4688
 	
  Answer: C:\windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create "net user /add A1berto paw0rd1

  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/49b1b67c-b973-449b-abe0-f6e6f4ec1a90)
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/3edb305c-c46c-4494-9bed-796335cbbbc3)

	6. How many times was the login attempt from the backdoor user observed during the investigation?
	
	Query: index=main User=A1berto
  Answer: 0

  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/3f248942-99a4-41f4-8028-f8322711edda)
  
	7. What is the name of the infected host on which suspicious Powershell commands were executed?
	
	I added command found from question #6 to filter out event. 
	Query: index=main EventID=4688 CommandLine="\"C:\\windows\\System32\\Wbem\\WMIC.exe\" /node:WORKSTATION6 process call create \"net user /add A1berto paw0rd1\""
  Answer: James.browne

  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/31cb2d53-8df3-422d-9b42-a6b234b25545)

	8. PowerShell logging is enabled on this device. How many events were logged for the malicious PowerShell execution?
	
	EventID 4103 and EventID 4104 are used for monitoring and logging PowerShell activity.
	Query: index=main EventID=4103 OR EventID=4104
  Answer: 79
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/52f29e32-a285-4346-8f47-186f52b773b1)

 	9. An encoded Powershell script from the infected host initiated a web request. What is the full URL?

  Answer: hxxp[://]10[.]10[.]10[.]5/news[.]php

  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/135137f3-a884-4ed3-838e-d50fe101cfa9)

  Paste the highlighted line to CyberChef to decode:
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/7617f3f4-3bce-49c0-a4db-fccd11e8025c)

  Decode again from Base64:
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/f1d1fd44-e94a-4c0c-8526-ef43e963b10d)

  Defang the URL using CyberChef:
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/1148af47-881a-494c-9ab3-03d5a7d8a284)

  Add the end of the URL:
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/302506cf-be6d-4f98-8b3a-34d47d47e93b)
  
  ![image](https://github.com/jli149/Investigating-with-Splunk/assets/52467584/439fcde4-f77e-4b82-ae20-e5cc3f268e82)


  






