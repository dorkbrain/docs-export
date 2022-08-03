---
title: "Subnets"
date: "2017-08-27"
categories: 
  - "misc"
tags: 
  - "misc"
---

<table><tfoot><tr><td colspan="4"><sup>* IP's Per Net include the network and broadcast IP</sup></td></tr></tfoot><thead><tr><td><strong>Network Mask</strong></td><td><strong>Mask Bits</strong></td><td><strong>Next Net</strong></td><td><strong>IP's Per Net *</strong></td></tr></thead><tbody><tr><td>255.0.0.0</td><td>8</td><td><strong>A+1</strong>.B.C.D</td><td>16,777,216</td></tr><tr><td>255.128.0.0</td><td>9</td><td>A.<strong>B+128</strong>.C.D</td><td>8,388,608</td></tr><tr><td>255.192.0.0</td><td>10</td><td>A.<strong>B+64</strong>.C.D</td><td>4,194,304</td></tr><tr><td>255.224.0.0</td><td>11</td><td>A.<strong>B+32</strong>.C.D</td><td>2,097,152</td></tr><tr><td>255.240.0.0</td><td>12</td><td>A.<strong>B+16</strong>.C.D</td><td>1,048,576</td></tr><tr><td>255.248.0.0</td><td>13</td><td>A.<strong>B+8</strong>.C.D</td><td>524,288</td></tr><tr><td>255.252.0.0</td><td>14</td><td>A.<strong>B+4</strong>.C.D</td><td>262,144</td></tr><tr><td>255.254.0.0</td><td>15</td><td>A.<strong>B+2</strong>.C.D</td><td>131,072</td></tr><tr><td>255.255.0.0</td><td>16</td><td>A.<strong>B+1</strong>.C.D</td><td>65,536</td></tr><tr><td>255.255.128.0</td><td>17</td><td>A.B.<strong>C+128</strong>.D</td><td>32,768</td></tr><tr><td>255.255.192.0</td><td>18</td><td>A.B.<strong>C+64</strong>.D</td><td>16,384</td></tr><tr><td>255.255.224.0</td><td>19</td><td>A.B.<strong>C+32</strong>.D</td><td>8,192</td></tr><tr><td>255.255.240.0</td><td>20</td><td>A.B.<strong>C+16</strong>.D</td><td>4,096</td></tr><tr><td>255.255.248.0</td><td>21</td><td>A.B.<strong>C+8</strong>.D</td><td>2,048</td></tr><tr><td>255.255.252.0</td><td>22</td><td>A.B.<strong>C+4</strong>.D</td><td>1,024</td></tr><tr><td>255.255.254.0</td><td>23</td><td>A.B.<strong>C+2</strong>.D</td><td>512</td></tr><tr><td>255.255.255.0</td><td>24</td><td>A.B.<strong>C+1</strong>.D</td><td>256</td></tr><tr><td>255.255.255.128</td><td>25</td><td>A.B.C.<strong>D+128</strong></td><td>128</td></tr><tr><td>255.255.255.192</td><td>26</td><td>A.B.C.<strong>D+64</strong></td><td>64</td></tr><tr><td>255.255.255.224</td><td>27</td><td>A.B.C.<strong>D+32</strong></td><td>32</td></tr><tr><td>255.255.255.240</td><td>28</td><td>A.B.C.<strong>D+16</strong></td><td>16</td></tr><tr><td>255.255.255.248</td><td>29</td><td>A.B.C.<strong>D+8</strong></td><td>8</td></tr><tr><td>255.255.255.252</td><td>30</td><td>A.B.C.<strong>D+4</strong></td><td>4</td></tr><tr><td>255.255.255.254</td><td>31</td><td>A.B.C.<strong>D+2</strong></td><td>2</td></tr><tr><td>255.255.255.255</td><td>32</td><td>A.B.C.<strong>D+1</strong></td><td>1</td></tr></tbody></table>

 

### [RFC-1918](https://tools.ietf.org/html/rfc1918) Private Address Ranges

| **Start** | **End** | **Network Mask** | **Mask Bits** | **Purpose** |
| --- | --- | --- | --- | --- |
| 10.0.0.0 | 10.255.255.255 | 255.0.0.0 | 8 | Private Class A |
| 169.254.0.0 | 169.254.255.255 | 255.255.0.0 | 16 | Automatic Private Addressing |
| 172.16.0.0 | 172.31.255.255 | 255.240.0.0 | 12 | Private Class B |
| 192.168.0.0 | 192.168.255.255 | 255.255.0.0 | 16 | Private Class C |
