:original_name: cce_faq_00406.html

.. _cce_faq_00406:

How Do I Configure the IPv6 Service CIDR Block When Creating a CCE Turbo Cluster?
=================================================================================

Context
-------

To create an IPv4/IPv6 dual-stack CCE Turbo cluster, you need to set an IPv6 Service CIDR block. The default CIDR block is **fc00::/112**, which contains 65536 IPv6 addresses. If you need to customize a Service CIDR block, you can refer to this section.

IPv6
----

**IPv6 address**

An IPv6 address is 128-bit binary string, four times the length of an IPv4 address. Therefore, the decimal format of IPv4 addresses is no longer applicable. IPv6 addresses are expressed in hexadecimal format. To convert a 128-bit binary string, it is transformed into a 32-bit hexadecimal string. These hexadecimal strings are grouped into sets of four (case insensitive) and separated by a colon (:). IPv6 addresses are divided into eight groups.

An IPv6 address can be omitted in the following ways:

-  Omission of leading 0s: 0s can be omitted if the colon group starts with 0s. The following IPv6 addresses are the same.

   -  ff01:**0**\ d28:**0**\ 3ee:**0000**:**0000**:**0000**:**0000**:**0**\ c23
   -  ff01:d28:3ee:**0000**:**0000**:**0000**:**0000**:c23
   -  ff01:d28:3ee:**0**:**0**:**0**:**0**:c23

-  Omission of all-0s hextets: You can use a double colon (::) to represent a single contiguous string of all-0s segments. A double colon (::) can be used only once.

   Example:

   ======================================== =================
   Before Omission                          After Omission
   ======================================== =================
   ff01:d28:3ee:**0**:**0**:**0**:**0**:c23 ff01:d28:3ee::c23
   0:0:0:0:0:0:0:1                          ::1
   0:0:0:0:0:0:0:0                          ``::``
   ======================================== =================

**IPv6 address segment**

An IPv6 address segment is usually expressed in Classless Inter-Domain Routing (CIDR) format. It is usually represented by a slash (/) followed by a number, that is, *IPv6 address/Prefix length*. The function of the prefix is similar to that of the mask of the IPv4 address segment. The number of binary bits occupied by the network part represents the binary bits occupied by the network part. An IPv6 address consists of the network part and host part. The prefix specifies the number of bits occupied by the network part, and the remaining bits are the host part.

For example, **fc00:d28::/32** indicates an IPv6 address segment with a 32-bit prefix. The first 32 bits (**fc00:d28** in binary mode) are the network part and the last 96 bits are available host part.

Constraints on IPv6 Service CIDR Blocks
---------------------------------------

When setting the cluster service CIDR block, note the following constraints:

-  The IPv6 Service CIDR block must belong to the **fc00::/8** CIDR block.

   The address is a unique local address (ULA). The ULA has a fixed prefix **fc00::/7**, including **fc00::/8** and **fd00::/8**. The two ranges are similar to the dedicated IPv4 network addresses **10.0.0.0/8**, **172.16.0.0/12**, and **192.168.0.0/16**. They are equivalent to private CIDR blocks and can be used only on the local network.

-  The prefix ranges from 112 to 120. You can adjust the number of addresses by adjusting the prefix value. The maximum number of addresses is 65536.

Example of an IPv6 Service CIDR Block
-------------------------------------

According to the constraints, this section provides an example of setting an IPv6 CIDR block that contains 8192 addresses for your reference.

#. Set the prefix length based on the number of addresses. The prefix length ranges from 112 to 120.

   In this example, 8,192 IP addresses are required, which are represented by 13-bit binary strings. An IPv6 address has a total length of 128-bit binary strings. As a result, the prefix length of the IPv6 CIDR block is 115 (128-13). This means that the first 115 bits are used to distinguish the CIDR block, while the last 13 bits indicate 8,192 host IP addresses.

   The following table shows how many IP addresses are there in an IPv6 CIDR block with the prefix ranging from 112 to 120.

   ============= ======================
   Prefix Length Number of IP Addresses
   ============= ======================
   112           65,536
   113           32,768
   114           16,384
   115           8,192
   116           4,096
   117           2,048
   118           1,024
   119           512
   120           256
   ============= ======================

#. Set the IPv6 network address, which must belong to the **fc00::/8** CIDR block.

   In this example, the prefix length is 115. Because the network address must belong to the **fc00::/8** CIDR block, the first 8-bit binary digits are fixed. The network address that can be modified ranges from the ninth bit to the 115th bit. The 116th bit to the 128th bit are the host part.

   If the IPv6 CIDR block is written in binary format, the following conditions are met:

   -  It must belong to **fc00::/8**, and the first eight bits in the binary string cannot be modified. Otherwise, the IPv6 CIDR block does not belong to **fc00::/8**. The CIDR block is fixed at **1111 1110**, corresponding to **fc** in hexadecimal format.
   -  The prefix length must be 115, and it must contain 8,192 IP addresses. The last 13 bits in the binary string are used to indicate the host IP address and are always 0.

   The following shows an example and the information in red cannot be modified:

   .. code-block::

      Binary: 1111 1100 **** **** ... ***0 0000 0000 0000/115
                   |    |    |    |        |    |    |    |
      Hexadecimal:   f    c    x    x ...    y    0    0     0/115

   **x** is a hexadecimal string. The last digit of the 4-bit binary string corresponding to **y** is always **0**. This means that the hexadecimal string **y** can be **0**, **2**, **4**, **6**, **8**, **a**, **c**, or **e**.
