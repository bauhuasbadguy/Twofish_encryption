# README #

This is a description of the Twofish encryption algorithm.

## IMPORTANT NOTE !!! ##

I am just some guy in a room, I have no training in cryptography and this code has not been checked by anyone with real training in cryptography. Just going through the code for this write up I found at least one major error in the implementation so please do not use this code for any real encryption purposes. I hope you will find this document informative and that the code may be helpful to you in understanding the Twofish algorithm but please do not attempt to use this code for real world encryption.

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
<image src = './g_function.png' width="600px;"></image>
</p>

Where the S-boxes are defined using the input key using the function shown below

<p align="center">
<image src = './h_function_on_side.png' width="800px;"></image>
</p>

### Decryption ###

### Sources ###

* 
