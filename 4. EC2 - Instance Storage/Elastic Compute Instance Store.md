- EBS volumes are network drives with good but "limited" performance
- if you need a high-performance hardware disk, use EC2 Instance Store

_EC2 Instance Store has a hard drive attached to a physical server_

Prós:
- Better I/O performance
Cons:
- EC2 Instance Store lose their storage if they're stopped (ephemeral)
Use cases:
- Buffer / Cache / Scratch data / Temporary Content

