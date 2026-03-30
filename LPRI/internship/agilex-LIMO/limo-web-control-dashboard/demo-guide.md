First open the terminal then select `foxy` by pressing `2` and `ENTER` . 
<img width="2887" height="1657" alt="image" src="https://github.com/user-attachments/assets/e6dc8847-cb17-4d4b-b95b-c700c3ca6e42" />
Then just run these commands : 
```
chmod +x ~/start_limo_dashboard.sh
~/start_limo_dashboard.sh
```
the process should start like this : 
<img width="2879" height="1658" alt="image" src="https://github.com/user-attachments/assets/aa49584d-01cf-49b3-996f-6f34cb053ce1" />
until all the processes run , 5 processes to be exact . 
then go into the website , the address should be `http://localhost:7000` then connect them using `ws://localhost:9090` and to get the localhost adress , 
just open a new terminal and run `hostname -I` the first adress should be it , that s it then you shoudl be connected to the robot and can control it from the web dashboard
and you should see something like this : 
<img width="2980" height="1836" alt="image" src="https://github.com/user-attachments/assets/df76cb00-a6fd-452e-9adf-ad2a47e0ea3f" />

for the technical details check out demo-final.md in this folder it has all the details so you can build this from scratch 

