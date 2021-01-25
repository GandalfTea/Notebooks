

### 1.1  Problem Domains

> _A problem domain is the part of the world where the computer is to produce effects, together with the means available to produce them, directly or indirectly_
(Kovitz, 1999)


A _problem domain_ is anything outside of programming that a software engineer needs to understand to produce correct code that can achieve the desired effects. _Directly_ means anything that the software can use to create the desired output, e. i. mouse, printer, etc.. _Indirectly_ means all that is not connected to the software, but is to the problem, e. i. notifying people, students that move to the correct classroom depending on the software, etc.  


For example, to write a finance application, you need to understand the financial concepts to fulfill the requirements of the customer. 

> _Requirements are the effect that the machine is to exert in the problem domain by virtue of its programming_

In order to implement useful software, not only knowledge of programming is important, but also of the problem domain. Problem domains may vary significantly. 

Often, learning the problem domain might be non-trivial and might require a bachelor degree or above to understand. It might be easier for someone already knowledgeble in the problem domain to start programming from scratch then the other way around.  

Software engineering is a problem domain of it's own. Operating Systems are a merging between two probem domains, Software and Electrical Engineering. You need to understand both in order to create one. 

How much learning is enough for a software engineer that wants to build an OS? Generally, you should be able to read the documents preared by the hardware engineers for using their devices.


### 1.2  Documents for implementing a problem domain


To facilitate the understanding of a problem domain, two documents need to be written : _software requirement document_ and _software specifications_.


#### 1.2.1 _Software Requirement Document_

They include both the list of requirements and a description of the problem domain. 

A software solved the problems described by the client. Most of the problems make a list of requirements that the program needs to solve. 

The quality of the software depends mostly on the programmers understanding of the problem domain. Because of this, along with the requirements the document should also contain a description of the problem domain. 

Software requirement document is all about the problem domain, it should not include a high-level description of the implementation. A good way to test the quality of the document is to give it to a domain expert for proofreading and making sure he understands everything thoroughly. 


#### 1.2.2 _Software Specification_

This documents relays the desired output for an input, as well as any rules that the program must obey.


Fr a hardware device, a specification document is always needed as the software depends on the behaviour of the hardware. It is usually the case that hardware specifications are well defined, with all details in. This is tied to the financial burden of producing faulty hardware rather than software. Similar to the requirement document, implementation details should not leak in. 


### 1.3  Documents for writing an x86 Operating System

page 9;
