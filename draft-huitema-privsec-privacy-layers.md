%%%
    Title = "Cross Layers Privacy Considerations"
    abbrev = "Cross Layers Privacy"
    category = "info"
    docName= "draft-huitema-privsec-privacy-layers-00.txt"
    ipr = "trust200902"
    area = "Network"
    date = 2017-05-09T00:00:00Z
    [pi]
    toc = "yes"
    compact = "yes"
    symrefs = "yes"
    sortrefs = "yes"
    subcompact = "no"
    [[author]]
    initials="C."
    surname="Huitema"
    fullname="Christian Huitema"
    organization = "Private Octopus Inc."
      [author.address]
      email = "huitema@huitema.net"
      [author.address.postal]
      city = "Friday Harbor"
      code = "WA  98250"
      country = "U.S.A"
%%%


.# Abstract

This document describes cross layers privacy issues.

{mainmatter}

# Introduction

# Examples of cross layer interactions

There are several documented examples of privacy mechanism that are implemented at one layer, but can be 
nullified by lack of coordination with other layers.

## MAC Address Randomization and IP addresses

MAC Address Randomization is a link layer mechanism for improving the privacy of a device.
The interaction between MAC Address Randomization and IP Address allocation is described in 
section 2.2 of the Anonymity Profile for DHCP Clients [@?RFC7844], which states:

   From a privacy point of view, it is clear that the link-layer
   address, IP address, and DHCP identifier shall evolve in synchrony.
   For example, if the link-layer address changes and the DHCP
   identifier stays constant, then it is really easy to correlate old
   and new link-layer addresses, either by listening to DHCP traffic or
   by observing that the IP address remains constant, since it is tied
   to the DHCP identifier.  Conversely, if the DHCP identifier changes
   but the link-layer address remains constant, the old and new
   identifiers and addresses can be correlated by listening to L2
   traffic.  The procedures documented in the following sections
   construct DHCP identifiers from the current link-layer address,
   automatically providing for this synchronization.

The mechanisms defined in [@?RFC7844] will ensure that IP addresses allocated through
DHCP do change when the MAC Address is randomized, but they do nothing for IPv6 addresses
allocated through Stateless Address Autoconfiguration [@?RFC4862]. There, the current
recommendation in [@?RFC8064] is to use the mechanisms defined in [@?RFC7217] when
allocating stable IPv6 addresses. This recommendation is an improvement over previous
practices such as embedding link layer addresses in the interface identifier (IID) part of
IPv6 addresses, or reusing the same IID in multiple networks. The text of [@?RFC8064]
clearly mentions the privacy issues with stable addresses, and states that the recommendation
only applies when stable addresses are desired. However, blindly
following the recommendations of [@?RFC7217] would keep the IPv6 address used on a
given network stable even if the MAC Address was randomized. This is a clear example
where lack of coordination between two layers would have detrimental effects on privacy.

## IPv6 temporary Addresses and HTTP cookies

The Privacy Extensions for Stateless Address Autoconfiguration in IPv6 [@?RFC4941] are
meant to protect user privacy when using IPv6. In a typical implementation, a host
will ge a new IPv6 address every day, and use it for that day only. The IID part
of the address will be made of random bytes, and in theory will be hard to correlate
with previous addresses used by the same host, found for example in network traces or
in server logs.

The obvious problem is that randomizing the IPv6 address does not help much if
the server logs contain other identifiers, such as for example the "cookies" used
in the HTTP protocol. Suppose that a host receives a new IPv6 address at the 
beginning of the day, and contacts again the same web server that it had contacted
the day before. The server will use the cookie to correlate the old and new IPv6
address, and the user will have gained little advantage from the mechanism in
[@?RFC4941].

Many Web Browsers have options to either "erase the browsing history", or to
switch to a "private" browsing mode in which they will not resend old cookies.
This sounds great from an HTTP point of view, but consider that same IPv6
address will be used before and after erasing the history or switching to
the private mode. Advertisers use complex and powerful tracking systems, 
such as defined in the Real Time Bidding Protocol [@RTB25], which use the
source IP address as an identifier of the web user. Since the address did 
not change, they will quickly use it to identify the user, even in the
absence of cookies. The browser privacy mechanisms will be nullified by the
lack of coordination with IPv6 address allocation.

## Domain name and SNI

Setting up a TLS encrypted connection is usually preceded by a DNS request for the
name of the service. Some servers host multiple services, each referenced by its
own domain name. During the TLS connection setup, the Service Name Information (SNI)
is used to direct the incoming connection to the right service. In the current
versions of TLS, the SNI is sent in clear text during the initial exchange. This
is rightly perceived as a privacy issue, since the SNI reveals the service
accessed by the user. There are efforts to fix this by finding some way to
encrypt the SNI.

Sending name service requests in clear text is also perceived as a privacy risk,
as discussed in [@?RFC7626]. This can be mitigated by sending DNS requests over a 
TLS protected connection, as defined in [@?RFC7858]. But few DNS
servers accept TLS connections today. There are many reasons for this slow
deployment, and one of them is probably the clear text SNI issue. Yes, 
sending the DNS requests over an encrypted connection will prevent adversaries
from watching the name of the services requested by the user. But the same
adversaries can then watch the user traffic, observe the SNI parameters of
outgoing TLS connection, and retrieve the information that DNS over TLS 
attempted to hide. 

Similarly, the limited deployment of DNS over TLS reduces the pressure to 
develop and deploy some form of SNI encryption. If the SNI was encrypted but
the DNS traffic was not, the adversaires would just observe the DNS queries
and obtain the list of services requested by the users.

Deploying either DNS over TLS or SNI encryption provide some value. For example,
SNI encryption reduces the risk of SNI based filtering or censorship. DNS 
over TLS can provide unfiltered access to name resolution services. But
one without the other is an incomplete solution, and incomplete solutions
do not get deployed quickly.



# Security Considerations

This document does not introduce any new protocol.
# IANA Considerations

# Acknowledgements

This document liberally borrows text from..

<reference anchor="RTB25" target="http://www.iab.com/wp-content/uploads/2016/03/OpenRTB-API-Specification-Version-2-5-FINAL.pdf">
  <front>
    <title>Real Time Bidding (RTB) project, OpenRTB API Specification Version 2.5</title>
    <author>
      <organization>Interactive Advertising Bureau (IAB)</organization>
    </author>
    <date year="2016" month="December"/>
  </front>
</reference>


{backmatter}


