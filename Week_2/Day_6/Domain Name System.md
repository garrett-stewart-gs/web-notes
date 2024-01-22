Although there are many available DNS Record Types, there is a small subset that cover most use cases.

  * A: most common; map a hostnames to IP address (IPv4, 32-bit address)
  * NS: Name Server that is responsible for a DNS zone
  * MX: Mail Exchange record specifies where email gets sent to
  * CNAME: Canonical Name, an alias for another hostname
  * AAAA: similar to A, but uses IPv6, 128-bit address

A DNS Request follows these stages:

  * root name servers - (*.root-servers.net)
  * continuing to the .com top level domain (TLD) 
  * followed by the google.com Name Servers (NS) - (ns*.google.com)
  * and concluded with Address Records (A) - (66.199.151.*)

"A" record typ maps host names to IPv4 Addresses
"AAAA" record type maps host names to IPv6 Addresses