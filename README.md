# README #

This is a description of the Twofish encryption algorithm.

## IMPORTANT NOTE !!! ##


## Algorithm description ##

The code in this repo can be devided into two sections, a subkey generation section and a encryption section.

### Key generation ###

In this section we are going to generate 40 subkeys as well as the s-boxes which will be used in the function g.

### Encryption ###


The function h defines the s-boxes in the function g

<p align="center">
<image src = './Twofish_encryption_structure_V1.png' width="600px;"></image>
</p>

The g-function shown here is defined as 

<p align="center">
<image src = './g_function.png' width="200px;"></image>
</p>

Where the S-boxes are defined using the input key using the function shown below



### Decryption ###

### Sources ###

* 
