# IDSCP2 Transport Layer

The IDSCP2 transport layer is a flexible and message agnostic transport layer, creating a TLS based tunnel and providing a state machine which enables flexible extension for various attestation mechanisms. The transport layer protocol negotiations have to be completed successfully before allowing the application layer to take over.

The heart of the IDSCP2 protocol is an event-based finite state machine that
consists of several states and transitions, which are triggered by events.

In the following, you can see a simplified version of the IDSCP2 state machine:

![](../images/FSM.png?raw=true)

The goal of the IDSCP2 handshake is to reach STATE_ESTABLISHED, which allows
the IDSCP2 communication with the remote peer. To achieve this state, first the handshake
has to be started by the upper layer, valid [DATs](../../../../Glossary#dynamic-attribute-token) have to be exchanged and verified, and finally
the Remote Attestation (RA) drivers have to verify each other's trusted state.

During the handshake as well as after the protocol is in STATE_ESTABLISHED,
re-attestations and fresh DAT requests can be triggered to keep the communication
trusted over time. Thus, many transitions in the state machine were introduced to support
both RA and DAT timeouts, upper layer re-attestation requests,
as well as re-attestation- and fresh-DAT-request from the remote peer
in each state.

In the following all states, events and timeouts are listed with a short description:

#### FSM states
- **STATE_CLOSED**, which should be divided into the final state, **STATE_CLOSED_LOCKED**,
  and into the init state, **STATE_CLOSED_UNLOCKED**.

- **STATE_WAIT_FOR_HELLO**, which waits for the IDSCP_HELLO of the remote peer

- **STATE_WAIT_FOR_RA**, which waits for the result of the RA_PROVER and RA_VERIFIER drivers

- **STATE_WAIT_FOR_RA_PROVER**, which waits for the result of the RA_PROVER driver

- **STATE_WAIT_FOR_RA_VERIFIER**, which waits for the result of the RA_VERIFIER driver

- **STATE_WAIT_FOR_DAT_AND_RA**, which waits for a fresh DAT of the remote peer, as well as for the RA_PROVER driver.
  After a valid DAT has been received, the RA_VERIFIER will be started for re-attestation.

- **STATE_WAIT_FOR_DAT_AND_RA_VERIFIER**, which waits for a fresh DAT of the remote peer. After
  a valid DAT has been received, the RA_VERIFIER will be started for re-attestation.

- **STATE_WAIT_FOR_ACK**, which waits for the ACK of the previous message.

- **STATE_ESTABLISHED**, which allows IDSCP2-secure communication with the remote peer.

#### FSM Events
In each of the above mentioned states, each possible FSM event should be handled.
The FSM events are structured into the following categories:

- 4 UPPER_EVENTS: Events from the User / Upper layer (e.g., ReRa, SendData)

- 6 Remote Attestation Events (RA_EVENTS): Events from the RaVerifier- and RaProverDriver (RA_OK, RA_FAILURE, RA_MSG)

- 10 Secure Channel Events (SC_EVENTS): Events from the underlying secure channel (Error, IDSCP2 messages from the remote peer)

- 4 TIMEOUT_EVENTS: Events triggered by Timer Routines (Handshake Timeout, RA Driver Timeouts, DAT Timeout, Ack Timeout)

In total, there are 24 theoretically possible events per state, which makes in sum 240 transitions
(10 states times 24 possible events). However, there is a significant number of events per state
that can to be ignored (e.g., almost all events in the final STATE_CLOSED_LOCKED) which reduces the transitions
to be implemented by about half of the theoretical 240.
Please note that ignoring important events in specific states can lead to security vulnerabilities. Thus, you can find a list
of important events for every state below in chapter "Detailed FSM state description".

In detail, the FSM includes the following 24 events:
- **UPPER_START_HANDSHAKE**: Start the IDSCP2 handshake for a fresh Idscp2Connection

- **UPPER_CLOSE**: Close the Idscp2Connection and lock the FSM forever

- **UPPER_SEND_DATA**: Send IDSCP_DATA (application data) to the remote peer

- **UPPER_RE_RA**: Repeat the RA verification of the remote peer

- **RA_VERIFIER_OK**: RA verification of the remote peer has succeed

- **RA_VERIFIER_FAILED**: RA verification of the remote peer has failed

- **RA_VERIFIER_MSG**: RA verifier driver has new data for the counterpart remote RA prover driver

- **RA_PROVER_OK**: RA prover has succeed

- **RA_PROVER_FAILED**: RA prover has failed

- **RA_PROVER_MSG**: RA prover driver has new data for the counterpart remote RA verifier driver

- **SC_ERROR**: Secure channel failed (e.g., socket IO error)

- **SC_IDSCP_HELLO**: Secure channel received IDSCP_HELLO from remote peer

- **SC_IDSCP_CLOSE**: Secure channel received IDSCP_CLOSE from remote peer

- **SC_IDSCP_DAT**: Secure channel received IDSCP_DAT from remote peer

- **SC_IDSCP_DAT_EXPIRED**: Secure channel received IDSCP_DAT_EXPIRED from remote peer

- **SC_IDSCP_RA_PROVER**: Secure channel received IDSCP_RA_PROVER from remote peer

- **SC_IDSCP_RA_VERIFIER**: Secure channel received IDSCP_RA_VERIFIER from remote peer

- **SC_IDSCP_RE_RA**: Secure channel received IDSCP_RE_RA from remote peer

- **SC_IDSCP_DATA**: Secure channel received IDSCP_DATA from remote peer

- **SC_IDSCP_ACK**: Secure channel received IDSCP_ACK from remote peer

- **HANDSHAKE_TIMEOUT**: A handshake timeout occurred. Lock the FSM forever

- **DAT_TIMEOUT**: A DAT timeout occurred, request a new DAT from remote peer

- **RA_TIMEOUT**: A RA timeout occurred, request re-attestation

- **ACK_TIMEOUT**: A ACK timeout occurred, send IDSCP_DATA again

#### Timeouts
The following timeouts exists:

* **RA Timeout:** This timeout occurs when the trust interval has been expired, and a new re-attestation of
  the remote peer should be requests.

* **DAT Timeout:** This timeout occurs when the DAT of the remote peer has been expired

* **Handshake Timeout:** This timeout occurs when the handshake activity takes to long. It is suggested
  to provide own handshake timers per RA driver, such that you can restrict the time a RA
  driver has to prove its trust.

* **Ack Timeout:** This timeout occurs when no IDSCP_ACK has been received for the last sent IDSCP_DATA
  message. It should trigger a repetition of sending the message.

### Detailed FSM state description
In the following, each state and its important events are discussed and what transitions
the specific event will trigger. If any unexpected error occurs, the FSM should go into STATE_CLOSED_LOCKED.

#### STATE_CLOSED_LOCKED
This state is actually the final state of the FSM without any outgoing
transition. If this state has been entered once, it can never ever reach another state again.
Therefore, an implementation in this state has to **ignore every event**.
This state should be entered from any other state whenever an error occurred, or
the connection lose its trust due to an invalid DAT, or a failed re-attestation.

#### STATE_CLOSED_UNLOCKED
This state is actually the init state of the FSM. The only event that should be
recognized in this state is the **UPPER_START_HANDSHAKE** event, which should send an IDSCP_HELLO
to the remote peer via the secure channel and should then go into the state **STATE_WAIT_FOR_HELLO**.

#### STATE_WAIT_FOR_HELLO
This state waits for the remote peer's IDSCP_HELLO, which contains the DAT and the
RA suites of the remote peer. When the SC_IDSCP_HELLO has been received, both connectors have to verify the
remote DAT via the DAPS driver, calculate the RA mechanisms and start the RA drivers.
A peer always decides which RA mechanism should be used to verify the remote peer. Thus, the outer loop
in the matching algorithm always has to loop through the RA_VERIFIERS, while the inner loop
goes through the counterpart RA_PROVERS.
In this state the handshake timer should be active.

The following events must be handled as described while all other events should be ignored:
- HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- SC_ERROR: go to STATE_CLOSED_LOCKED

- SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

- SC_IDSCP_HELLO: If the DAT is valid, start the DAT timeout, calculate the RA mechanisms,
  start the RA drivers, start the driver handshake timeouts and go to STATE_WAIT_FOR_RA. Otherwise,
  send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

#### STATE_WAIT_FOR_RA
This state waits for the results of the RA_PROVER, as well as the RA_VERIFIER driver.
The driver handshake timer, as well as the DAT timer should be active.

The following events must be handled as described while all other events should be ignored:
- HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- DAT_TIMEOUT: send IDSCP_DAT_EXPIRED and go to STATE_WAIT_FOR_DAT_AND_RA

- UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- RA_PROVER_OK: cancel the RA_PROVER handshake timer and go to STATE_WAIT_FOR_RA_VERIFIER

- RA_PROVER_MSG: send IDSCP_RA_PROVER and stay in STATE_WAIT_FOR_RA

- RA_PROVER_FAILED: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- RA_VERIFIER_OK: cancel the RA_VERIFIER handshake timer and go to STATE_WAIT_FOR_RA_PROVER

- RA_VERIFIER_MSG: send IDSCP_RA_VERIFIER and stay in STATE_WAIT_FOR_RA

- RA_VERIFIER_FAILED: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- SC_ERROR: go to STATE_CLOSED_LOCKED

- SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

- SC_IDSCP_DAT_EXPIRED: send a fresh IDSCP_DAT, restart RA_PROVER and stay in STATE_WAIT_FOR_RA

- SC_IDSCP_RA_PROVER: delegate to local RA_VERIFIER driver and stay in STATE_WAIT_FOR_RA

- SC_IDSCP_RA_VERIFIER: delegate to local RA_PROVER driver and stay in STATE_WAIT_FOR_RA

- SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag and alternate the next_send_alternating_bit, otherwise ignore. Stay in STATE_WAIT_FOR_RA

#### STATE_WAIT_FOR_RA_VERIFIER
This state waits for the result of the RA_VERIFIER driver. The RA_VERIFIER handshake timer, as well as
the DAT timer should be active.

The following events must be handled as described while all other events should be ignored:
- HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- DAT_TIMEOUT: send IDSCP_DAT_EXPIRED and go to STATE_WAIT_FOR_DAT_AND_RA_VERIFIER

- UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- RA_VERIFIER_OK: cancel the RA_VERIFIER handshake timer and go to STATE_ESTABLISHED or STATE_WAIT_FOR_ACK
  depending on if the ack flag is set or not.

- RA_VERIFIER_MSG: send IDSCP_RA_VERIFIER and stay in STATE_WAIT_FOR_RA_VERIFIER

- RA_VERIFIER_FAILED: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- SC_ERROR: go to STATE_CLOSED_LOCKED

- SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

- SC_IDSCP_DAT_EXPIRED: send a fresh IDSCP_DAT, restart RA_PROVER and stay in STATE_WAIT_FOR_RA

- SC_IDSCP_RA_PROVER: delegate to local RA_VERIFIER driver and stay in STATE_WAIT_FOR_RA_VERIFIER

- SC_IDSCP_RE_RA: restart the RA_PROVER driver and go to STATE_WAIT_FOR_RA

- SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag and alternate the next_send_alternating_bit, otherwise ignore. Stay in STATE_WAIT_FOR_RA_VERIFIER

#### STATE_WAIT_FOR_RA_PROVER
This state waits for the result of the RA_PROVER driver. The RA_PROVER handshake timer, as well as
the DAT timer, and the RA timer should be active. In this state, the remote peer has already been verified.

The following events must be handled as described while all other events should be ignored:
- HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- RA_TIMEOUT: request re-attestation by sending IDSCP_RE_RA, start the RA_VERIFIER and its handshake timeout
  and go to STATE_WAIT_FOR_RA

- DAT_TIMEOUT: send IDSCP_DAT_EXPIRED and go to STATE_WAIT_FOR_DAT_AND_RA

- UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- UPPER_RE_RA: request re-attestation by sending IDSCP_RE_RA, start the RA_VERIFIER and its handshake timeout
  and go to STATE_WAIT_FOR_RA

- RA_PROVER_OK: cancel the RA_PROVER handshake timer and go to STATE_ESTABLISHED or STATE_WAIT_FOR_ACK
  depending on if the ack flag is set or not.

- RA_PROVER_MSG: send IDSCP_RA_PROVER and stay in STATE_WAIT_FOR_RA_PROVER

- RA_PROVER_FAILED: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

- SC_ERROR: go to STATE_CLOSED_LOCKED

- SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

- SC_IDSCP_RE_RA: restart the RA_PROVER driver and stay in STATE_WAIT_FOR_RA_PROVER

- SC_IDSCP_DAT_EXPIRED: send a fresh IDSCP_DAT, restart RA_PROVER and stay in STATE_WAIT_FOR_RA_PROVER

- SC_IDSCP_RA_VERIFIER: delegate to local RA_PROVER driver and stay in STATE_WAIT_FOR_RA_PROVER

- SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag and alternate the next_send_alternating_bit, otherwise ignore. Stay in STATE_WAIT_FOR_RA_PROVER


#### STATE_WAIT_FOR_DAT_AND_RA_VERIFIER
In this state the local peer first has to wait for a fresh valid DAT from the remote peer. Only the
handshake timer should be active. No RA driver should be active.

The following events must be handled as described while all other events should be ignored:
* HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* SC_ERROR: go to STATE_CLOSED_LOCKED

* SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

* SC_IDSCP_DAT_EXPIRED: send a fresh IDSCP_DAT, start the RA_PROVER driver and go to STATE_WAIT_FOR_DAT_AND_RA

* SC_IDSCP_DAT: if the DAT is valid, start the DAT timeout, start the RA_VERIFIER driver and go to STATE_WAIT_FOR_RA_VERIFIER

* SC_IDSCP_RE_RA: start the RA_PROVER driver and go to STATE_WAIT_FOR_DAT_AND_RA

* SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag and alternate the next_send_alternating_bit, otherwise ignore. Stay in
  STATE_WAIT_FOR_DAT_AND_RA_VERIFIER

#### STATE_WAIT_FOR_DAT_AND_RA
This state is similar to the STATE_WAIT_FOR_DAT_AND_RA_VERIFIER, but the RA_PROVER
driver is active. Only the handshake timer should be active.

The following events must be handled as described while all other events should be ignored:
* HANDSHAKE_TIMEOUT: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* RA_PROVER_OK: cancel the RA_PROVER handshake timer and go to STATE_WAIT_FOR_DAT_AND_RA

* RA_PROVER_MSG: send IDSCP_RA_PROVER and stay in STATE_WAIT_FOR_DAT_AND_RA

* RA_PROVER_FAILED: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* SC_ERROR: go to STATE_CLOSED_LOCKED

* SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

* SC_IDSCP_DAT_EXPIRED: send a fresh IDSCP_DAT, restart the IDSCP_RA_PROVER and stay in
  STATE_WAIT_FOR_DAT_AND_RA

* SC_IDSCP_DAT: if the DAT is valid, start the DAT timeout, start the RA_VERIFIER driver and go to
  STATE_WAIT_FOR_RA

* SC_IDSCP_RA_VERIFIER: delegate to local RA_PROVER driver and stay in STATE_WAIT_FOR_DAT_AND_RA

* SC_IDSCP_RE_RA: restart the RA_PROVER driver and stay in STATE_WAIT_FOR_DAT_AND_RA

* SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag and alternate the next_send_alternating_bit, otherwise ignore. Stay in
  STATE_WAIT_FOR_DAT_AND_RA

#### STATE_ESTABLISHED
This state is the goal state, which allows secure and trusted communication between
two peers. The DAT timeout and the RA timeout should be active in this state.

The following events must be handled as described while all other events should be ignored:
* UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* UPPER_RE_RA: send IDSCP_RE_RA, start RA_VERIFIER driver and RA_VERIFIER handshake timer and
  go to STATE_WAIT_FOR_RA_VERIFIER

* UPPER_SEND_DATA: send IDSCP_DATA with next_send_alternating bit, message, set ACK flag, start ACK timer
  and go to STATE_WAIT_FOR_ACK

* RA_TIMEOUT: send IDSCP_RE_RA, start RA_VERIFIER driver and RA_VERIFIER handshake timer and
  go to STATE_WAIT_FOR_RA_VERIFIER

* DAT_TIMEOUT: cancel RA timer, send IDSCP_DAT_EXPIRED and go to STATE_WAIT_FOR_DAT_AND_RA_VERIFIER

* SC_ERROR: go to STATE_CLOSED_LOCKED

* SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

* SC_DAT_EXPIRED: send a fresh IDSCP_DAT, restart the local RA_PROVER and go to
  STATE_WAIT_FOR_RA_PROVER

* SC_RE_RA: start the RA_PROVER driver and go to STATE_WAIT_FOR_RA_PROVER

* SC_IDSCP_DATA: If the received alternating bit within the IDSCP_DATA is not equal to the
  expected_alternating bit, ignore the message. Otherwise, send an IDSCP_ACK containing the expected_alternating
  bit to the remote peer, then alternate the expected_alternating bit and pass the message to the upper layer.

#### STATE_WAIT_FOR_ACK
This state is a kind of established state, however before new messages can be sent, the peers
have to wait for the expected IDSCP_ACK that belongs to our previous sent message. This is required to
guarantee that packages cannot be lost during a re-attestation of the trusted state.
The IDSCP_ACK follows the AlternatingBit protocol, which is explained below. An ACK timer will be active
to trigger repetition of sending data when no ACK has been received within a specified interval.
Further, the RA timer and the DAT timer should be active.

The following events must be handled as described while all other events should be ignored:
* UPPER_CLOSE: send IDSCP_CLOSE and go to STATE_CLOSED_LOCKED

* UPPER_RE_RA: cancel ACK timer, send IDSCP_RE_RA, start RA_VERIFIER driver and RA_VERIFIER handshake timer and
  go to STATE_WAIT_FOR_RA_VERIFIER

* RA_TIMEOUT: cancel ACK timer, send IDSCP_RE_RA, start RA_VERIFIER driver and RA_VERIFIER handshake timer and
  go to STATE_WAIT_FOR_RA_VERIFIER

* DAT_TIMEOUT: cancel RA timer and ACK timer, send IDSCP_DAT_EXPIRED and go to STATE_WAIT_FOR_DAT_AND_RA_VERIFIER

* ACK_TIMEOUT: repeat sending the cached IDSCP_DATA, restart the ACK timer and stay in STATE_WAIT_FOR_ACK

* SC_ERROR: go to STATE_CLOSED_LOCKED

* SC_IDSCP_CLOSE: go to STATE_CLOSED_LOCKED

* SC_DAT_EXPIRED: cancel ACK timer, send fresh IDSCP_DAT, restart the RA_PROVER driver and
  go to STATE_WAIT_FOR_RA_PROVER

* SC_RE_RA: cancel ACK timer, start the RA_PROVER driver and go to STATE_WAIT_FOR_RA_PROVER

* SC_IDSCP_DATA: If the received alternating bit within the IDSCP_DATA is not equal to the
  expected_alternating bit, ignore the message. Otherwise, send an IDSCP_ACK containing the expected_alternating
  bit to the remote peer, then alternate the expected_alternating bit and pass the message to the upper layer.

* SC_IDSCP_ACK: if this ACK belongs to the expected message regarding the alternating bit protocol,
  clear the ack flag, alternate the next_send_alternating_bit and go to STATE_ESTABLISHED, otherwise ignore
  it and stay in STATE_WAIT_FOR_ACK

### Alternating Bit Protocol
The alternating bit protocol is responsible for guaranteeing that no IDSCP_DATA can get lost
during a re-attestation procedure. Each peer has one **next_send_alternating_bit** for sending
IDSCP_DATA without loss and one **expected_alternating_bit** for receiving IDSCP_DATA in the right order.

At the beginning, both bits must be initialized with zero. When sending new IDSCP_DATA, the
**next_send_alternating_bit** is placed into the IDSCP_DATA message. It is only then alternated, when
an IDSCP_ACK with this same **next_send_alternating_bit** has been received.

The **expected_alternating_bit** is used for checking if a new received IDSCP_DATA message has correctly
been ordered and no other IDSCP_DATA between has been lost between. When the bit of the received IDSCP_DATA
matches the **expected_alternating_bit**, an IDSCP_ACK is sent with this
**expected_alternating_bit** and will be alternated afterwards.

### Ra Mechanism Calculation
As already described above, each peer has to decide its local RA_VERIFIER mechanism, which is
then used by the local RA_VERIFIER, as well as by the corresponding remote RA_PROVER to verifier that the
remote peer is in a trusted state.
The local RA suites are passed by the user to the IDSCP core, while the remote suites are received
via the IDSCP_HELLO, which holds a list of supported and expected RA mechanisms. These lists are
ordered by the priority, which means that early entries should be preferred.

An example for the implementation can be found [here](./Examples.md#ra-mechanism-calculation)

### IDSCP2 message format
The IDSCP2 messages have been built via Protobuf version 3.
The following format must be respected in all implementations:

```proto
syntax = "proto3";

//IDSCP message frame
message IdscpMessage {
    // One of the following will be filled in.
    oneof message {
        IdscpHello idscpHello = 1;
        IdscpClose idscpClose = 2;
        IdscpDatExpired idscpDatExpired = 3;
        IdscpDat idscpDat = 4;
        IdscpReRa idscpReRa = 5;
        IdscpRaProver idscpRaProver = 6;
        IdscpRaVerifier idscpRaVerifier = 7;
        IdscpData idscpData = 8;
        IdscpAck idscpAck = 9;
    }
}


//IDSCP messages
message IdscpHello {
    int32 version = 1;                      //IDSCP protocol version
    IdscpDat dynamicAttributeToken = 2;     //initial dynamicAttributeToken
    repeated string supportedRaSuite = 3;  //RemoteAttestationCipher prover
    repeated string expectedRaSuite = 4;   //RemoteAttestationCipher verifier
}

message IdscpClose {

    enum CloseCause {
        USER_SHUTDOWN = 0;
        TIMEOUT = 1;
        ERROR = 2;
        NO_VALID_DAT = 3;
        NO_RA_MECHANISM_MATCH_PROVER = 4;
        NO_RA_MECHANISM_MATCH_VERIFIER = 5;
        RA_PROVER_FAILED = 6;
        RA_VERIFIER_FAILED = 7;
    }

    CloseCause cause_code = 1;
    string cause_msg = 2;
}

message IdscpDatExpired {}

message IdscpDat {
    bytes token = 1;
}

message IdscpReRa {                
    string cause = 1;               
}

message IdscpRaProver {
    bytes data = 1;
}

message IdscpRaVerifier {
    bytes data = 1;
}

message IdscpData {                 
    bytes data = 1;
    bool alternating_bit = 2;
}

message IdscpAck {
    bool alternating_bit = 1;
}
```
