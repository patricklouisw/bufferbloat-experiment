# Submission

Mean time recorded when queue size is 20:
average: 0.611466666667 ms
standard deviation: 0.229596806017 ms

Mean time recorded when queue size is 100:
average: 1.39193939394 ms
standard deviation: 0.763257529889 ms

## Questions

1. Why do you see a difference in webpage fetch times with small and large router buffers?

2. Bufferbloat can occur in other places such as your network interface card (NIC). Check the output of ifconfig eth0 on your VirtualBox VM. What is the (maximum) transmit queue length on the network interface reported by ifconfig? For this queue size and a draining rate of 100 Mbps, what is the maximum time a packet might wait in the queue before it leaves the NIC?

3. How does the RTT reported by ping vary with the queue size? Write a symbolic equation to describe the relation between the two (ignore computation overheads in ping that might affect the final result).

4. Identify and describe two ways to mitigate the bufferbloat problem.

---
## Answers
1.  Using TCP Reno congestion control algorithm, we know that at the "slow start" stage, it will double our congestion window each RTT until it detects a packet loss in order to maximise our bandwidth usage and it will drop the congestion window to half when it detects a packet loss.

    In our experiment, we are fully utilising the bandwidth towards h2 by opening a Long-lived TCP connection. 

    In a smaller router buffer, the buffer fills up fast and start dropping packets to signal the sender to reduce its congestion window. Hence, traffic are managed early, reducing fetch time.

    However, in the larger router buffer, the buffer takes a while to be filled up and sender will keep on doubling its congestion window without knowing that the traffic is congested. Hence, every packets in the buffer essentially are delayed to be routed to its destination. Hence, the longer fetch time.

2.  a. Maximum Transmit queue length: 1000 packets

    b.  __Given:__ MTU = 1500 Bytes; Draining rate = 100Mbps

        Maximum time a packet might wait in the queue before it leaves the NIC:
        1500 * 1000 * 8 bits / (100 * 10^6) bps = 0.12 seconds

3.  I added a code in plot_ping.py to calculate the mean RTT found.

Mean of queue size 20: 44.4900525762 ms
Mean of queue size 100: 223.566805846 ms

RTT ~= 5 * QUEUE_SIZE + d

d = deviation from any delays (transmission, propagation, processing, queueing delay)

4.  Two ways to mitigate bufferbloat:

    a. A buffer packet management algorithm which preemptively selects packets to be dropped from the buffer so as to alert the sender to reduce its congestion window, hence reducing traffic rate.

    b. Identify the traffic flow based on the service it provides and sets a rate in which each TCP flows are allowed to send its packet. Hence, we are shaping the incoming traffic flow as such that we will not fully utilised the buffer and caused a bufferfloat.

