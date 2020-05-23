# README #

This is a description of the Twofish encryption algorithm. Really it is the description of Twofish 128, 192 and 256 which are versions of Twofish with different key lengths. I am writing this to improve my understanding and also because I have found other descriptions of this algorithm I have found online to be confusing (there may be errors in what I have written). If you are trying to understand the Twofish algorithm I hope you will find this repo useful but this should not be used for encryption purposes.

## IMPORTANT NOTE !!! ##

I am just some guy in a room, I have no training in cryptography and this code has not been checked by anyone with real training in cryptography. Just going through the code for this write up I found at least one major error in the implementation so please do not use this code for any real encryption purposes. I hope you will find this document informative and that the code may be helpful to you in understanding the Twofish algorithm but please do not attempt to use this code for real world encryption.

## Algorithm description ##

The code in this repo can be devided into two sections, a subkey generation section and a encryption section.

### Key generation ###

In this section we are going to first generate three important vectors, M<sub>e</sub>, M<sub>o</sub> and S. The M vectors we are then going to use to generate the subkeys, K<sub>j</sub> and the S vector will be used in the encryption and decryption of the input text.

The process for generating these vectors changes slightly depending on whether we are working with Twofish 128, 192 or 256. The length of these vectors will be determined by the value of &sigma; which is defined below

&sigma; = N/64

where N is the length of the key. We are now going to convert the key M into 2&sigma; words of 32 bits each. This will be the vector M<sub>i</sub>. These will then be used to generate the vectors M<sub>e</sub> and M<sub>o</sub> using the logic shown below

M<sub>e</sub> = (M<sub>0</sub>, M<sub>2</sub>, ..., M<sub>2&sigma;-2</sub>)
M<sub>o</sub> = (M<sub>1</sub>, M<sub>3</sub>, ..., M<sub>2&sigma;-1</sub>)

The third word vector, S is henerated using a matrix multiplication in the GF(2<sup>8</sup>) field. First a 8 element vector is created from the vector m, which is the key split into bytes (8 bit words). Now the words of the S vector will be generated using the logic shown below (in an png image because I can't get matrix mutliplication to look right in markdown)

<p>
<image src = './S_vector_matmul..png' width="400px;"></image>
</p>



### Encryption ###


The function h is used to define the keys and what are (incorectly?) labeled S-boxes in the paper

<p align="center">
<image src = './Twofish_encryption_structure_V1.png' width="600px;"></image>
</p>

The g-function shown here is defined as 

<p align="center">
<image src = './g_function.png' width="600px;"></image>
</p>

Where the S-boxes are defined using the input key using the function shown below

<p align="center">
<image src = './h_function_on_side.png' width="100%;"></image>
</p>

### Decryption ###

### Sources ###

* 
