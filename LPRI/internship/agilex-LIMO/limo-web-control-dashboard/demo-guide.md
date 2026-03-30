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




# LIMO Web Dashboard — Quick Run Guide

## 1. Open the terminal and select Foxy

Open a terminal, then select **`foxy`** by pressing:

- `2`
- `ENTER`

<img width="2887" height="1657" alt="image" src="https://github.com/user-attachments/assets/e6dc8847-cb17-4d4b-b95b-c700c3ca6e42" />

---

## 2. Run the startup script

Execute the following commands:

```bash
chmod +x ~/start_limo_dashboard.sh
~/start_limo_dashboard.sh
````

This will start the full dashboard system.

---

## 3. Wait for all processes to start

You should see the processes launching in separate terminals.

* Wait until **all 5 processes** are running.

<img width="2879" height="1658" alt="image" src="https://github.com/user-attachments/assets/aa49584d-01cf-49b3-996f-6f34cb053ce1" />

---

## 4. Open the dashboard in the browser

Go to:

```text
http://localhost:7000
```

If you are accessing it from another device, use the robot/laptop IP address instead of `localhost`.

---

## 5. Connect the dashboard to ROS

Inside the dashboard, use:

```text
ws://localhost:9090
```

If you are connecting from another device on the same network, replace `localhost` with the actual machine IP.

---

## 6. Find the machine IP address

To get the correct local IP address, open a new terminal and run:

```bash
hostname -I
```

Use the **first IP address** shown in the output.

Example:

```text
172.20.43.207
```

Then:

* Dashboard URL becomes:

```text
http://172.20.43.207:7000
```

* ROSBridge URL becomes:

```text
ws://172.20.43.207:9090
```

---

## 7. Control the robot

Once connected, the dashboard should be linked to the robot and ready for control.

You should see something like this:

<img width="2980" height="1836" alt="image" src="https://github.com/user-attachments/assets/df76cb00-a6fd-452e-9adf-ad2a47e0ea3f" />

---

# Notes

* Make sure all 5 processes are running before trying to connect.
* If using another device, both devices must be on the same network unless a remote-access solution is configured.

---

# Technical details

For the full technical setup and implementation details, check:

```text
demo-final.md
```

in this same folder.

That file contains the information needed to understand or rebuild the project from scratch.


