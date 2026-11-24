---
cssclasses:
  - dds-notes
---
A link represents the network components of the distributed system: every pair of processes is connected by a bidirectional link. Under the crash-failure assumption we consider three links: **fair-loss, stubborn and perfect**.

>[!note] Remark
> Deliver is different from receive. A message is received at a port of the network and stored in a buffer; then an algorithm runs to make sure that the properties of the required link abstraction hold, and only afterwards is the message delivered.

###### Fair-loss links
Messages can get lost, but if a message is continuously re-transmitted, under certain conditions it is delivered.
###### Stubborn links
They guarantee continuous re-transmission of messages until they are delivered, so the same message can be received multiple times.
###### Perfect links
They ensure that messages are delivered reliably, without duplication and without creating non-existent messages.