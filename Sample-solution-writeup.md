Process 
First of all the i'm thinking like attacker/hacker as well as i'm doing some CTF for my cybersecurity study so i know some of the things like methodology so what i think after saw the diagram 
i did everything step by step.

1. i have the IAM user which is dev_user(attacker) on public subnet
2. Finding the private subnet EC2 by using dev_user which can i modified that EC2 inside the private. actually its working i can stop and start that so i got the idea i can access the private EC2
3.After i found EC2 access i'm checking we have any endpoint database/s3 if we have any endpoint so we can access the some of the file from the database/s3 also i check any policy on any bucket so nothing on that
4. We found the 2 bucket inside that EC2 then looking inside i got the 1 sensitive file which is .txt so that mean i can access that without any admin role/ with limited privilage
   that means i got access which is unauthorized access

5. Most of important think i use cheetsheet from github for the command i know the what i need to do step by step but i don't know the commands so i attached the link of that cheet-sheet pdf
   https://github.com/studybox9999-cmd/AWS-cheet-sheet/blob/main/aws_cli_cheatsheet.pdf

Reflection:

1. Firstly i learn the AWS cli
2. I have question which i will do afterwards to find what is the misconfigure while create the private subnet and IAM user role related because I’m still confuse what is the main problem/loophole
    which can give access to the normal user with limited permission.
3. I noticed and learn from this small mistake can do big trouble
4. I wanted to try more things inside like what we can modified like chnage on our user policy and we can add/remove any the IAM User -> we upload/inject the malicious code/virus in the bucket/database and
    download the all of Any company and i can host any live web services which is not legal and run spread any virus/malicious stuff. I want do more deeper what the hacker can do extremely if he is inside the network
5.Most of important things now i know where the cloud architecture do mistake and we can find/try/fix as cloud security person.

     Thank you Sir For this amazing Lab So i can learn something new.
   
