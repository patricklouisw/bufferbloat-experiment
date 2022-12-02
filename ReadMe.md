# Submission
q20:
average: 0.586266666667
standard deviation: 0.141697706403

q100
average: 1.11255555556
standard deviation: 0.582599082867

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
        1500 * 1000 * 8 / (100 * 10^6) = 0.12 seconds

3. RTT ~= 6 * QUEUE_SIZE + C

C = Propagation Delay + Packetization Delay ~= 20ms


4.  One way to mitigate buffer bloat would be to differentiate TCP flows based on the Differentiated Services flags in the TCP header. This would only mitigate the problem by using multiple smaller buffers.

    Another way to mitigate the problem is to employ active queue management algorithms. These algorithms intelligently choose to preemptively drop or mark packets prior to the queue filling up, preventing a long lived TCP flow from blocking other flows.


