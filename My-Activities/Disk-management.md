## Create a volume and attach to the AWS instance

- Create a volume
- Attach a volume to EC2 instance
- Create a directory and format the block device using mkfs

- Create a volume in AWS, i haven't added any tags. before that make sure ec2 is in ap-south-1a as volume also shoud in same availablity zone. while doing this activity i created wrongly in 1a, post that i created in 1b

<img width="1498" height="855" alt="image" src="https://github.com/user-attachments/assets/4fc93278-2575-4053-a48f-34f41224b175" />
<img width="1752" height="734" alt="image" src="https://github.com/user-attachments/assets/94a1c44a-39c5-47cd-ae3b-caa4cce09e17" />
<img width="1894" height="510" alt="image" src="https://github.com/user-attachments/assets/ba24eeeb-c564-4626-8646-b11b9140be20" />
<img width="1918" height="751" alt="image" src="https://github.com/user-attachments/assets/a270e6cf-0908-42be-80b6-082c9e6c159f" />

- Now it should be in available state

<img width="1919" height="665" alt="image" src="https://github.com/user-attachments/assets/3d6a3c3c-4a05-4049-9234-65bbe3c1cd1d" />

- Attach a volume to EC2 instance

<img width="1919" height="851" alt="image" src="https://github.com/user-attachments/assets/b5fcc84c-4e89-4e48-9a4e-1b926c31ac7b" />

- we can see new volume in availble in EC2

<img width="976" height="408" alt="image" src="https://github.com/user-attachments/assets/02be431e-5af0-4ce5-ab3a-6fafe8252957" />

- Now to use this block storage we need to format it and mount it

- create the directory and format it

<img width="922" height="497" alt="image" src="https://github.com/user-attachments/assets/254ec86d-d536-4557-8af3-4ba6bcd07c38" />

- Now mount the volume

<img width="847" height="623" alt="image" src="https://github.com/user-attachments/assets/e2e578b7-8855-44d2-80b6-a656979f47a9" />


