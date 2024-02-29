# <b><u>Content:</u></b>
1. [[COM stack based on CAN.md#^1. <b><u>intro:</u></b>|#Intro]]
2. [[COM stack based on CAN.md#^2. <b><u>AUTOSAR layered architecture:</u></b>|#AUTOSAR layered architecture]]
3. [[COM stack based on CAN.md#^3. <b><u>COM stack concepts and terminologies:</u></b>|#COM stack concepts and terminologies]]
4. [[COM stack based on CAN.md#^4. <b><u>Modules Roles and features:</u></b>|#Modules Roles and features]]
	I. [[COM stack based on CAN.md#^I. <b><u>Com (Communication module):</u></b>|#Com (Communication module)]]
		1. [[COM stack based on CAN.md#^1. <b><i><u>Role:</u></i></b>|#Role]]
		2. [[COM stack based on CAN.md#^2. <b><i><u>Features:</u></i></b>|#Features]]
			1. [[COM stack based on CAN.md#^1. <b><u><i>Communication mode:</i></u></b>|#Communication mode]]
				a. [[COM stack based on CAN.md#^a. <b><u><i>Transmission mode (of the PDU):</i></u></b>|#Transmission mode (of the PDU)]]
				b. [[COM stack based on CAN.md#^b. <b><u><i>Transfer property (of the signal and signal group):</i></u></b>|#Transfer property (of the signal and signal group)]]
			2. [[COM stack based on CAN.md#^2. <b><u><i>Com filter (when receiving):</i></u></b>|#Com filter (when receiving)]]
			3. [[COM stack based on CAN.md#^3. <b><u><i>Update bit:</i></u></b>|#Update bit]]

# 1. <b><u>intro:</u></b>

![[CAN_network.png]]
***COM stack*** is used to communicate between the ECUs so, lets consider the following scenarios:
1. the <b><u><i>Master</i></u></b> ECU is sending a ***==periodic==*** message that contains the following ***==signals==***: kilometers, Speedand time stamp to all the other ECUs where these ECUs use that data when taking a snapshot if any problem happens, so, for that we need the COM stack.
2. <b><u><i>ECU_1</i></u></b> sends this data that contains the gear state to <b><u><i>ECU_2</i></u></b> and according to the gear state if it's ***R (Reverse state)*** the camera should be functioning to let the user see what is behind the car.
there are more scenarios of course than those 2, so, COM stack is one of the most important stacks in the CAR.
there are more than one type of COM stack as follows:
1. COM stack based on ***==CAN==***.
2. COM stack based on ***==Ethernet==***.
3. COM stack based on ***==LIN==***.
4. COM stack based on ***==flexRay==***.
but we will focus on the <b><u><i>COM stack based on CAN</i></u></b>.
***==Note:==*** the requirements of the communication stack is prepared by the <b><i><u>OEM</u></i></b> in the form of ***communication matrix (CAN DB - CAN data base)*** where this matrix contains number of messages, which message is sent by which ECU, how many signals are there in each message, the size of these signals and so on.
***==Note:==*** there are tools for requirement named ***requirement management tools*** and one of the famous tools <b><i><u>Doors</u></i></b>.

![[Car_networks_example.png]]
more than one communication protocol can be in the same ECU and this ECU can be used as gateway between the ECUs that are communicating through different protocols and hence this ECU is named ***==Gateway ECU==***.
according to the pic. above the used ***Gateway ECU*** has 3 communication protocols which are ***CAN***, ***Ethernet***, ***LIN***, to be able to translate between those in the CAN network, LIN network and Ethernet network.
# 2. <b><u>AUTOSAR layered architecture:</u></b>

![[COM_stack_architecture_AUTOSAR.png]]
this diagram shows the whole architecture including more than communication protocol module as ***FlexRay***, ***CAN***, ***LIN***, but we will focus on the ***==CAN==***.

![[COM_stack_architecture_based_on_CAN.png]]
in this diagram the COM stack architecture based on ***CAN*** where we can split the modules into 2 groups:
1. ***Network control path:*** where the communication path is opened or closed, so, it is                                                         represented as the muscles.
2. ***Data path:*** where the logic of transporting the data whether collecting them through Com                          module, routing the PDUs through the PduR and so on (the functionality of the                          Com module and the PduR will be covered later).


# 3. <b><u>COM stack concepts and terminologies:</u></b>

1. <b><u><i>SDU</i></u></b>: Service Data Unit (raw data)
2. <b><u><i>PCI</i></u></b>: Protocol Control Information (some data relative to the protocol itself)
3. <b><u><i>UD</i></u></b>: User Data.
4. <b><u><i>PDU</i></u></b>: Packet Data Unit
        `PDU = PCI + UD + SDU`.
	
	![[PDU_PCI_SDU.png]]
	***==Note==***: every ***PDU*** is transported from layer to the layer below as ***SDU*** where some ***PCI*** is                    added to this ***SDU*** to form a new ***PDU*** that will be transported again to the next layer              where it will be treated as ***SDU*** again and so on.
5. <b><u><i>I-PDU</i></u></b>: Interaction Layer PDU, where it's the PDU in the service layer above the                                       Communication hardware abstraction layer(EcuAL)
6. <b><u><i>N-PDU</i></u></b>: Network Layer PDU, where it's the PDU in the EcuAL.
7. <b><u><i>L-PDU</i></u></b>: Data link layer PDU, where it's the PDU in the MCAL.
8. <b><u><i>Signals</i></u></b>: Signals are the smallest entities for the exchange of information.
9. <b><u><i>Signal group</i></u></b>: logical grouping of some signals (i.e., if there is more than one signals that                                 should be sent at the same time or we can say that the receiver will need them                           all at once as the environment data of the car like the speed and so on, so, these                        signals are grouped together in one signal group).
	![[Signal_signalGroup.png]]
	as shown in this pic the SWC (Software Component) in the Application layer communicates only in signals and signal groups then send these signals and signal groups to the RTE (Run-Time Environment) where the RTE forwards these signals and signal groups to a modules named ***==Com==*** where those signals and signal groups are packed into a single PDU
	`PDU = Signals + Signal gorups` and the ***PDU*** can be consisted of signals only or signal groups only.
# 4. <b><u>Modules Roles and features:</u></b>

## I. <b><u>Com (Communication module):</u></b>
### 1. <b><i><u>Role:</u></i></b>

### 2. <b><i><u>Features:</u></i></b>
#### 1. <b><u><i>Communication mode:</i></u></b>
##### a. <b><u><i>Transmission mode (of the PDU):</i></u></b>
- <u><i><b>Direct</b></u></i>: ***Application*** is the one which takes the decision to send the PDU (incase the PDU is                     configured to be Direct), Where the ***APP*** tells the ***Com module*** through ***RTE*** to send this             PDU.
		![[Direct_transmission_mode.png]]
		***==Note==***: sending a **PDU** configured to be in ***Direct mode*** will rely on the <b><u><i>transfer property</i></u></b>             of its signals.
		
- <u><i><b>Periodic</b></u></i>: Com module is responsible of sending the PDU periodically (incase the PDU is                             configured to be periodic), where the Com module has its own timers to calculate this                time in order to send this PDU periodically.
		![[Periodic_transmission_mode.png]]
		
- <u><i><b>Mixed</b></u></i>: it is a mix of the 2 modes above where the ***Com module*** sends the PDU every                              configured period and also the ***App*** through the ***RTE*** tells the ***Com module*** to send this              PDU at a certain time, so, the ***Com module*** is responsible of sending this PDU in the                    configured periods and also if the ***App*** wants to send it in any time other than the                       period that is configured in the ***Com module*** it will contact the ***Com module*** through                  the ***RTE*** and tells it to send the PDU. 
		![[Mixed_transmission_mode.png]]
##### b. <b><u><i>Transfer property (of the signal and signal group):</i></u></b>
- <u><i><b>Triggered (or Triggered on update)</b></u></i>: if a signal is configured as triggered on update then this                                                                 means that if there is a **PDU** that has this signal that is                                                                   configured as ***triggered*** and the ***App*** sends this signal to                                                                the ***Com module*** then the whole ***PDU*** will be transmitted                                                               from the ***Com module*** to the ***PduR*** ==immediately== regardless                                                            of the other signals in that ***PDU***.

- <u><i><b>Triggered on change</b></u></i>: if a signal is configured as triggered-on-change signal this means that if                                       there is a ***PDU*** that has this signal that is configured as ***triggered-on-                                           change*** and the ***App*** sends this signal to the ***Com module*** then the                                               whole ***PDU*** will be transmitted from the ***Com module*** to the ***PduR*** iff ***==the==                                      ==value of this signal has changed from the last time it was sent==***.
	an example on <b><u><i>triggered on update</i></u></b> and <b><u><i>triggered-on-change</i></u></b>:
	
	![[triggered_triggered_on_change_signals.png]]
	if we have a **PDU** as shown in the pic. above where this **PDU** consists of 3 signals: 
		- <b><u><i>Signal #1</i></u></b>: triggered signal.
		- <b><u><i>Signal #2</i></u></b>: triggered signal.
		- <b><u><i>Signal #3</i></u></b>: triggered-on-change signal.
	so, lets consider the following 4 scenarios:
	<b><u>First:</u></b>
	![[sending_triggered_signal_1.png]]
	as shown in this pic. ***==Signal #1==*** is been sent from ***APP_1*** to the ***Com module*** through the ***RTE***  and since ***==Signal #1==*** is ***==triggered signal==*** then this ***PDU*** should be sent immediately from the ***Com module*** to the ***PduR*** regardless the value of this signal is been changed from the last time it was sent at and regardless of whether the other signals are sent or not.
	<b><u>Second:</u></b>
	![[Sending_triggered_signal_2.png]]
	as shown in this pic. ***==Signal #2==*** is been sent from ***APP_1*** to the ***Com module*** through the ***RTE***  and since ***==Signal #2==*** is ***==triggered signal==*** then this ***PDU*** should be sent immediately from the ***Com module*** to the ***PduR*** regardless the value of this signal is been changed from the last time it was sent at and regardless of whether the other signals are sent or not.
	<b><u>Third:</u></b>
	![[Sending_triggered_on_change_signal_changed.png]]
	as shown in this pic. ***==Signal #3==*** is been sent from ***APP_1*** to the ***Com module*** through the ***RTE***  and since ***==Signal #3==*** is ***==triggered-on-change signal and its value is being changed from the last time it was sent (i.e., its value now equal 10 and its previous value was 0 this means its value changed)==*** then this ***PDU*** should be sent immediately from the ***Com module*** to the ***PduR*** regardless of whether the other signals are sent or not.
	<b><u>Forth:</u></b>
	![[Sending_triggered_on_change_signal_unchanged.png]]
	as shown in this pic. ***==Signal #3==*** is been sent from ***APP_1*** to the ***Com module*** through the ***RTE***  and since ***==Signal #3==*** is ***==triggered-on-change signal and its value isn't been changed from the last time it was sent (i.e., its value now equal 10 and its previous value was also 10 this means its value is the same)==*** then this ***PDU*** <b><i><u>shouldn't</u></i></b> be sent from the ***Com module*** to the ***PduR*** and this ***PDU*** should <b><i><u>wait</u></i></b> till one of the other triggered signals (i.e., signal #1 or signal #2) is sent or this triggered-on-change signal (i.e., signal #3) is sent again but with different value.
	<b><u><i>But why there is triggered and triggered-on-change?</i></u></b>
	this have a very important reason why if we have a signal that must be sent immediately but iff its value is different from the last time, so, in this case the ***Com modules*** checks if its value changed and if it's changed then the ***Com module*** will send it immediately, but if not then the **PDU** won't be sent and save the bus from sending unimportant **PDU**.
	this differs from the triggered signals where whether its value is changed or not the **PDU** must be sent immediately and this ***==depends on the system design of the car==***.
	
- <u><i><b>Pending</b></u></i>: this transfer property indicates that this signal or signal group won't be sent except                    the ***I-PDU*** is periodic or there is any other signal or signal group that is triggered or                    triggered on change. ***==So, it can't be that the I-PDU has all of its signals and signal==***                     ***==groups of the transfer property pending as it won't be transferred whatever happens==***.

<b><u><i>But what about signal groups?</i></u></b>
Signal groups are treated as the signals where they have the same transfer properties **triggered on update**, **triggered on change** and ***pending***. 
So, lets consider the following scenario:
we have an ***I-PDU*** that consists of one ***signal group*** where this ***signal group*** consists of 2 signals and the requirements is that when the 2 signals in that signal group is updated then send this signal group. So, according to this requirement the transfer property of the signals and the signal group should be as follows:
- ***==The 2 signals==***: should be <b><u><i>pending</i></u></b>.
- ***==The signal group==***: should be <b><u><i>triggered</i></u></b>.
***==Note==***: the signal group would be sent when the following calls (From the RTE to the Com module) happens:
- `Com_SendSignal(Signal_1_ID , &Signal_1_data)`.
- `Com_SendSignal(Signal_2_ID , &Signal_2_data)`.
- `Com_SendSignalGroup(SignalGroup_ID)`.
#### 2. <b><u><i>Com filter (when receiving):</i></u></b>
this feature filters the received ***signals*** according to the following criterion or lets say filtering types, this is applied on each signal received in the ***I-PDU***.

##### a. <b><u><i>Always (no-filter):</i></u></b>
here any signal inside the ***I-PDU*** will be sent to the corresponding ***App*** after notifying them via ***RTE***

##### b. <b><u><i>Signal value within range:</i></u></b>
this means whenever the received signal is within a configured range then ***==notify==*** the ***App*** that there is a signal received through the ***RTE***.
But, if the signal value is outside that configured range then ***==neglect==*** this signal(i.e., don't notify the ***App***).

##### c. <b><u><i>Signal value outside range:</i></u></b>
this means if the received signal value is outside a configured range then ***==notify==*** the ***App*** that there is a signal received through the ***RTE***.
But, if the signal value is within the configured range then ***==neglect==*** this signal(i.e., don't notify the ***App***).

##### d. <b><u><i>Equal to:</i></u></b>
this means that if the received signal value is equal to a specific configured value then ***==notify==*** the ***App*** that there is a signal received through the ***RTE***.
But, if the signal value is not equal this configured value then ***==neglect==*** the signal(i.e., don't notify the ***App***).

##### d. <b><u><i>Not equal to:</i></u></b>
this means that if the received signal value is not equal to a specific configured value then ***==notify==*** the ***App*** that there is a signal received through the ***RTE***.
But, if the signal value is equal to this configured value then ***==neglect==*** the signal(i.e., don't notify the ***App***).


#### 3. <b><u><i>Update bit:</i></u></b>
it is a single bit attached to the signal when configured, where this bit indicates that the **==Signal ,where this update bit is configured to represent, is updated(i.e., sent from the App to the Com module through the RTE)==** the word ***updated*** here means that it is sent from the **App** to the ***Com module*** through the ***RTE***, so, either the signal value is changed from the previous one or not, the update bit will be ***==set==***.
![[Update_bit.png]]
***==Note==***: this ***I-PDU*** is ***direct*** and those ***signals*** are ***triggered***.
this diagram shows that if the update bit feature is configured in the ***Com module*** then the signals that are configured to have these update bits will be sent with their update bits.
if the **App** sends a signal that to the ***Com module*** then its update bit will be ***==set==*** then the ***Com module*** will send this ***I-PDU*** to the ***PduR***.
The ***Com module*** clears this update bit according to the configuration where we have 2 cases:
- clearing the update bit once the ***Com module*** is sent to the ***PduR***.
- clearing the update bit after receiving an ***ACK*** that this ***PDU*** is being received.
<b><u><i>But why there is update bit?</i></u></b>
this feature is provided by the ***AUTOSAR*** to protect the receiver ECU from hustle of processing the other signals in the ***PDU*** that aren't updated, so, the receiver ECU will check the update bit, if configured, if they are set then the receiver ECU will process this signal if no set then it won't process the signal.


# 5. <b><u>Full pic. from the app to the physical bus (Sending):</u></b>

![[Sending_PDU_BlockDiagram.png]]

# 6. <b><u>Full pic. from the physical bus to the App (Receiving):</u></b>


# 6. <b><u>Sequence diagrams:</u></b>



# 7. <b><u>Extra info:</u></b>
[Com module](https://www.linkedin.com/pulse/com-module-classic-autosar-mikio-hirai/)
