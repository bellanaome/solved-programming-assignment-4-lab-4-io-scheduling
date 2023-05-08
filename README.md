Download Link: https://assignmentchef.com/product/solved-programming-assignment-4-lab-4-io-scheduling
<br>
You are to implement different IO-schedulers in C or C++ and submit the <strong>source </strong>code, which we will compile and run. Your submission must contain a Makefile so we can run on <strong>crackle*.cims.nyu.edu</strong> (and please at least test there as well).

In this lab you will implement/simulate the scheduling of IO operations. Applications submit their IO requests to the IO subsystem, where they are maintained in an IO-queue. The IO-scheduler then selects a request from the IO-queue and submits it to the disk. On completion another request can be taken from the IO-queue and submitted to the disk. The scheduling policies will allow for some optimization as to reduce disk head movement or overall wait time in the system. The schedulers to be implemented are FIFO (i), SSTF (j), LOOK (s), CLOOK (c), and FLOOK (f) (the letters in bracket define which parameter must be given in the –s program flag).




Invocation is a follows:

./iosched –s&lt;schedalgo&gt; &lt;inputfile&gt;




The input file is structured as follows: Lines starting with ‘#’ are comment lines and should be ignored.

Any other line describes an IO operation where the 1<sup>st</sup>  integer is the time step at which the IO operation is issued and the 2<sup>nd</sup> integer is the track that is accesses. Since IO operation latencies are largely dictated by seek delay (i.e. moving the head to the correct track), we ignore rotational and transfer delays for simplicity. Move by one track takes one time unit. The inputs are well formed.




#io generator

#numio=32 maxtracks=512 lambda=10.000000

1 430

129 400 :




We assume that moving the head by one track will cost one time unit. As a result your simulation can/should be done using integers. The disk can only consume/process one IO request at a time. Everything else must be maintained in an IO queue and managed according to the scheduling policy. The initial direction of the LOOK algorithms is from 0-tracks to higher tracks. The head is initially positioned at track=0 at time=0. Note that you do not have to know the maxtrack (think SCAN vs. LOOK).




Each simulation should track on individual IO requests followed by a SUM line that has computed the some statistics of the overall run. (see reference outputs).




For each IO request create a following line (5 shown).

0:     1     1   431

1:    87   467   533

2:   280   431   467

3:   321   533   762

4:   505   762   791




Created by

printf(“%5d: %5d %5d %5d
”, i, req-&gt;arrival_time, r-&gt;start_time, r-&gt;end_time); –   IO-op# ,

<ul>

 <li>its arrival to the system (same as inputfile)</li>

 <li>its disk start time</li>

 <li>its disk end time</li>

</ul>







and for the complete run a SUM line:




total_time:         total simulated time, i.e. until the last I/O request has completed. tot_movement: total number of tracks the head had to be moved

avg_turnaround: average turnaround time per operation from time of submission to time of completion avg_waittime: average wait time per operation, i.e. time from submission to issue of the IO request to start of disk operation

max_waittime:      maximum wait time for any IO operation.




Created by

printf(“SUM: %d %d %.2lf %.2lf %d
”,

total_time,    tot_movement,   avg_turnaround,    avg_waittime,    max_waittime);




Various sample input and outputs are provided on the website.

Please look at the sum results and identify what different characteristics the schedulers exhibit.




You can make the following assumptions:

– at most 10000 IO operations will be tested, so its OK to first read all requests from the file before processing.  – you never have two IO requests arrive at the same time




I suggest, you don’t use discrete event simulation. You can write a loop that increments simulation time by one and checks whether any action is to be taken. In that case you have to check in the following order.

<ul>

 <li>Did a new I/O arrive to the system at this time, if so add to IO-queue</li>

 <li>Is an IO active and completed at this time</li>

 <li>Is an IO active but did not complete, then move the head by one sector/track/unit in the direction it is going (to simulate seek)</li>

 <li>Is no IO request active now (after (2)) but IO requests are pending? Fetch and start a new IO.</li>

</ul>




When switching queues in FLOOK you always continue in the direction you were going from the current position, until the queue is empty then switch direction until empty and then switch the queues. While other variants are possible, I simply chose this one this time.




<u>Additional Information:</u>




As usual, I provide some more detailed tracing information to help you overcome problems. Note your code only needs to provide the ‘SUM line’ and the result line per IO request from above.




In addition I provide three options –v, -q, -f to debug deeper into IO tracing and IO queues.




The <strong>–v </strong>execution trace contains 3 different operations (<strong>add</strong> a request to the IO-queue, <strong>issue</strong> an operation to the disk and <strong>finish</strong> a disk operation). Following is an example of tracking IO-op 21 through the times 2827..2892 from submission to completion.




2827:    21 add 204                 //  21 is the IO-op # (starting with 0) and 204 is the track# requested

2827:    21 issue 204 269        //  21 is the IO-op #, 204 is the track# requested, 269 is the current track#

2892:    21 finish 65                 //  21 is the IO-op #, 65 is total length of the io from request to completion




Finally, I provide a summary line for each io-request which consolidates the trace into a single line per IO, providing the following information:







<strong>-q</strong> shows the details of the IO queue and <strong>–f</strong> shows additional queue information during the FLOOK.

Queue entries are tuples during add [ ior# : #io-track ] or triplets during get [ ior# : io-track# : distance ],  where distance is negative if it goes into the opposite direction (where applicable )




To get to these, log into course machines at NYU and use ~frankeh/Public/iosched   reference program.