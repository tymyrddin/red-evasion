# Signature identification 

Identifying signatures, whether manually or automated, involves employing an iterative process to determine what 
byte a signature starts at. Recursively splitting a compiled binary in half and testing it, gives a rough estimate of a 
byte-range to investigate further.

Signature identification can be automated using scripts to split bytes over an interval for us. 


Find-AVSignature will split a provided range of bytes through a given interval.

