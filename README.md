# SDN Network DDoS detection using machine learning

# Setup

1- Download any version of ubuntu server, in my case 19.04.

2- Download anaconda to ubuntu server and install it.

3- Install ryu controller on ubuntu server using pip: pip install ryu.

4- Download mininet and install hping3 on it.

5- Test connecivity.

# Generate dataset:

1- Copy generate_benign_traffic.py, generate_ddos_traffic.py, topology.py to mininet using ssh as follow:

scp generate_benign_traffic.py generate_ddos_traffic.py topology.py mininet@192.168.x.x:/home/mininet/sdn-ddos-detection.

2- Copy collect_benign_traffic.py, collect_benign_traffic.py, controller.py to controller (ubuntu server) using ssh as follow:

scp collect_benign_traffic.py collect_benign_traffic.py controller.py ryu@192.168.x.x:/home/ryu/sdn-ddos-detection.

3- Collect benign traffic by launching collect_benign_traffic.py on controller and generate_benign_traffic.py on mininet as follow:

-> ryu-manager collect_benign_traffic.py (on controller)

-> sudo python generate_benign_traffic.py (on mininet)

4- After collecting benign traffic, collect ddos traffic by launching collect_ddos_traffic.py on controller and generate_ddos_traffic.py on mininet as follow:

-> ryu-manager collect_ddos_traffic.py (on controller)

-> sudo python generate_ddos_traffic.py (on mininet)

# Machine learning and testing:

1- Learn from dataset generated and predict flow (legitimate traffic or ddos traffic every 10 seconds)

-> ryu-manager controller.py (on controller)

-> sudo python topology.py (on mininet)

2- Test legitimate traffic, on mininet:

-> xterm h1 h5 h10 h15 h18

-> ping 10.0.0.x (0<x<19) on h1 h5 h10 h15 h18

-> (h1) mkdir webserver; cd webserver; "create index.html"; python -m SimpleHTTPServer 80

-> (on any host between h1 and h18) mkdir Downloads; cd Downloads; wget http://10.0.0.1/index.html

3- Test ddos traffic, on mininet:

-> (on any host between h1 and h18) hping3 -1 --rand-source --flood 10.0.0.x (ICMP Flooding)

-> (on any host between h1 and h18) hping3 -2 --rand-source --flood 10.0.0.x (UDP Flooding)

-> (on any host between h1 and h18) hping3 -S -p 80 --rand-source --flood 10.0.0.1 (SYN Flooding)

-> (on any host between h1 and h18) hping3 -1 --flood -a 10.0.0.1 10.0.0.1 (LAND Attack)

Tips:

1- Use ssh to connect to controller and mininet as foloow:

-> ssh -X mininet@192.168.x.x

-> ssh ryu@192.168.x.x
