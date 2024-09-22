## Incident Response: Brute Force Success – Windows Incident

 # Incident ID 66: Brute Force Success - Windows
 
   # Step 1: Preparation
In this phase, logs were already ingested into Log Analytics Workspace and Microsoft Sentinel was configured with alert rules to detect specific activities, including brute force attempts. The workspace identified Incident ID 66, with a high severity alert concerning a potential brute force attack.

![see Screenshot 1](https://i.imgur.com/mpEGVYS.jpeg)

  # Step 2: Detection & Analysis

## 1. Alert Overview and Entity Details

In Microsoft Sentinel , Incident ID 66 was flagged as a Brute Force Success event on a Windows VM. The source of the alert comes from windows-vm, with the identified IP being 186.96.10.36. The system shows 5 triggered alerts associated with this incident. The description mentions that a successful brute force attempt occurred with an NT AUTHORITY\ANONYMOUS LOGON, which was suspicious.

![see Screenshot 2 & 3](https://i.imgur.com/tGKsSUE.jpeg)

## 2. Full Investigation

The timeline of events shows repeated login attempts, and in the entity view, both the windows-vm and the source IP (186.96.10.36) were tracked in this attack. Further investigation reveals that NT AUTHORITY\ANONYMOUS LOGON appeared multiple times. This aligns with known brute force patterns .

![see Screenshot 4 & 5](https://i.imgur.com/tGKsSUE.jpeg)

![see Screenshot 4 & 5](https://i.imgur.com/8j4TfQ8.jpeg)

Using the Investigation tool, I traced the relationships between the different events, illustrating a series of connections between brute force attempts (both successes and failures) across multiple entities. The red lines indicate high-severity incidents involving this particular VM and attacker IP.

![see Screenshot 6 ](https://i.imgur.com/SrUi5JE.jpeg)

![see Screenshot 7](https://i.imgur.com/1A4HZlM.jpeg)

## 3. Log Analysis
![see Screenshot 8](https://i.imgur.com/OtaDkHZ.jpeg)

We ran detailed queries in the Log Analytics Workspace . One query tracked successful logon attempts with EventID 4624 and matched them against failed logons from the same attacker IP. This query shows repeated successful logins from 186.96.10.36 targeting windows-vm. This further solidified the evidence of a successful brute force attack.

![see Screenshot 9](https://i.imgur.com/D4wGq5i.jpeg)
![see Screenshot 10](https://i.imgur.com/KrKOtHt.jpeg)

  # Step 3: Containment, Eradication, and Recovery
  
   ## 1. Initial Containment Measures

I immediately initiated actions to mitigate the impact by locking down the network security group (NSG) tied to the VM. Specifically, the NSG's inbound rules were inspected, and I identified a  rule titled DANGERAllowAllInbound . This rule allowed all traffic inbound without restriction, creating a serious security vulnerability.

![see Screenshot 11](https://i.imgur.com/JO8LwV3.jpeg)

I applied a new NSG rule allowing  IP (172.58.45.62/32), ensuring no further inbound traffic would reach the VM apart from this address.

![see Screenshot 13](https://i.imgur.com/Roh5kLk.jpeg)

   ## 2. Password Reset and MFA Implementation

To prevent further unauthorized access, I reset the affected user's password and enabled multi-factor authentication (MFA) for the affected system users, making future brute force attempts significantly more difficult to execute.

# Step 4: Document Findings and Close the Incident

## 1. Classification of the Incident

After conducting a full investigation, I determined that this specific brute force alert, Incident ID 66, was a false positive. Despite multiple successful logon attempts being logged, these were caused by a service account and not an actual malicious actor.

## 2. Closing the Incident

Finally, I classified this incident as a False Positive - Incorrect Alert Logic (, and closed it within Microsoft Sentinel (Screenshot 16). Though it was a false positive, I initiated actions to harden the NSGs across the affected VM infrastructure to prevent potential real brute force attacks in the future.

![see Screenshot 15](https://i.imgur.com/glpKXdG.jpeg)

# Conclusion

This incident highlighted key steps in incident response, from detection through investigation and containment. The Configuration of a NSG was crucial, and though the alert was ultimately a false positive, the corrective actions taken will help strengthen the system’s defense against future attacks.
