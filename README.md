# README #

This is a description of the Twofish encryption algorithm. Really it is the description of Twofish 128, 192 and 256 which are versions of Twofish with different key lengths. I am writing this to improve my understanding and also because I have found other descriptions of this algorithm I have found online to be confusing (there may be errors in what I have written). If you are trying to understand the Twofish algorithm I hope you will find this repo useful but this should not be used for encryption purposes. This code only has support for 128 bit keys, if I can be bothered at some point in the future I will add support for 192 and 256 bit keys.

## IMPORTANT NOTE !!! ##

I am just some guy in a room, I have no training in cryptography and this code has not been checked by anyone with real training in cryptography. Just going through the code for this write up I found at least one major error in the implementation so please do not use this code for any real encryption purposes. I hope you will find this document informative and that the code may be helpful to you in understanding the Twofish algorithm but please do not attempt to use this code for real world encryption.

## Algorithm description ##

The code in this repo can be devided into two sections, a subkey generation section and a encryption/decryption section.

### Key generation ###

#### Generate the preliminary vectors ###

In this section we are going to first generate three important vectors, M<sub>e</sub>, M<sub>o</sub> and S. The M vectors we are then going to use to generate the subkeys, K<sub>j</sub> and the S vector will be used in the encryption and decryption of the input text.

The process for generating these vectors changes slightly depending on whether we are working with Twofish 128, 192 or 256. The length of these vectors will be determined by the value of &sigma; which is defined below

&sigma; = N/64

where N is the length of the key. We are now going to convert the key M into 2&sigma; words of 32 bits each. This will be the vector M<sub>i</sub>. These will then be used to generate the vectors M<sub>e</sub> and M<sub>o</sub> using the logic shown below

M<sub>e</sub> = (M<sub>0</sub>, M<sub>2</sub>, ..., M<sub>2&sigma;-2</sub>)
M<sub>o</sub> = (M<sub>1</sub>, M<sub>3</sub>, ..., M<sub>2&sigma;-1</sub>)

The third word vector, S is henerated using a matrix multiplication in the GF(2<sup>8</sup>) field. First a 8 element vector is created from the vector m, which is the key split into bytes (8 bit words). Now the words of the S vector will be generated using the logic shown below (in an png image because I can't get matrix mutliplication to look right in markdown)

<p>
<image src = './S_vector_matmul.png' width="350px;"></image>
</p>

The 4, 8 bit words generated by this function are combined into a single 32 bit word which is added to the S vector until &sigma; 32 bit words have been generated. This multiplication is done within the finite field GF(2<sup>8</sup>). This means that any mathematical function must take place within the finite field GF(2<sup>8</sup>), the process for which is described in the following sub-sub-sub-section.

##### Multiplication in GF(2<sup>8</sup>) #####

The field GF(2<sup>8</sup>) is a finite field containing 2<sup>8</sup> elements, also known as the Galois field. This field is the ring of integers 2<sup>8</sup> long. Within this ring you can perform the operations of addition, subtraction and multiplication but the input and the results must always be contained within the field.

Addition in this field is performed using a XOR operation, doubling is done using a bitwise shift to the left and halving is done using a bitwise shift to the right. Knowing this we can use Russian peasant multiplication in order to find a way to perform multiplication within GF(2<sup>8</sup>)

Russian peasant multiplication is an algorithm for performing multiplication whereupon you can multiply two numbers A and B. In this algorithm we will first intialise p as zero before starting the first round of the multiplication process.

 With each round we first check if B is odd, if it is we add A to p (using XOR since we are working in GF(2<sup>8</sup>)). Next we double A, using a rightwise bitshift, and check if it is outside GF(2<sup>8</sup>). If A is outside GF(2<sup>8</sup>) we will bring the value of A back inside the field by XORing A with the primitive polynomial, valued at 283 for GF(2<sup>8</sup>). Next we will half B using a bitshift to the left, if this results in B being equal to zero we end the algorithm and return p as the result. Otherwise we go back to the start of the loop and perform another round.


#### The h-function ####

Now we need to introduce the function h which will be used to generate the subkeys from the vectors M<sub>e</sub> and M<sub>o</sub> and which will also be used in combination with the S vector in the encryption step of the algorithm. The structure of the function h is shown below

<p align="center">
<image src = './h_function_on_side.png' width="100%;"></image>
</p>

In this diagram the fact that the round 3, 4 and 5 are the only rounds which will always be used is demonstrated using the switches connecting round 2 and 1 to the rest of the blocks. When &sigma; is greater than 3, i.e. key length is 192 or 256, then round 2 is used and if &sigma; is equal to 4, i.e. key length is 256, then all rounds are used. The blocks q<sub>0</sub> and q<sub>1</sub> are permutation blocks which work on 8 bit words. They follow the logic show below where x is the input to the block and y is the output.

a<sub>0</sub>, b<sub>0</sub> = [x/16], x % 16

a<sub>1</sub> = a<sub>0</sub> &bigoplus; b<sub>0</sub>

b<sub>1</sub> = a<sub>0</sub> &bigoplus; ROR<sub>4</sub>(b<sub>0</sub>, 1) &bigoplus; 8a<sub>0</sub> % 16

a<sub>2</sub>, b<sub>2</sub> = t<sub>0</sub>\[a<sub>1</sub>\], t<sub>1</sub>\[b<sub>1</sub>\]

a<sub>3</sub> = a<sub>2</sub> &bigoplus; b<sub>2</sub>

b<sub>3</sub> = a<sub>2</sub> &bigoplus; ROR<sub>4</sub>(b<sub>2</sub>, 1) &bigoplus; 8a<sub>2</sub> % 16

a<sub>4</sub>, b<sub>4</sub> = t<sub>2</sub>\[a<sub>3</sub>\], t<sub>3</sub>\[b<sub>3</sub>\]

y = 16b<sub>4</sub> + a<sub>4</sub>

The logic is the same for both q<sub>0</sub> and q<sub>1</sub> but the substitution boxes, t<sub>i</sub>, are different in q<sub>0</sub> and q<sub>1</sub>.

#### Generation of the subkeys ####

Finally using the intermediary vectors and the function h we may generate the subkeys K<sub>j</sub> by using the logic chain shown below

&rho; = 2<sup>24</sup> + 2<sup>16</sup> + 2<sup>8</sup> + 2<sup>0</sup>

A<sub>i</sub> = h(2i&rho;, M<sub>e</sub>)

B<sub>i</sub> = ROL(h(2i + 1, M<sub>0</sub>), 8)

K<sub>2i</sub> = (A<sub>i</sub> + B<sub>i</sub>) % 2<sup>32</sup>

K<sub>2i + 1</sub> = ROL(A<sub>i</sub> + 2B<sub>i</sub>) % 2<sup>32</sup>, 9)

In this way the 40 subkeys are generated which will be used in combination with the S vector in order to perform both encryption and decryption.

### Encryption ###

The general shape of the encryption function is shown below.

<p align="center">
<image src = './Twofish_encryption_structure_V2.png' width="600px;"></image>
</p>

In this diagram you can see the subkeys K<sub>0-3</sub> being used as an initial whiteining step as the 128 bit input is broken into 4, 32 bit blocks. Then the left hand side words are put through h blocks with the S vector resulting in two 32 bit words which are crossed with one another in a PHT block which uses modulo addition to combine them. The results are then modulo added to the subkeys K<sub>2r+8</sub> and K<sub>2r + 9</sub> where r is the round number. The results from this are XORed with segments from the right hand side of the word after a ROL by one bit is applied to word R<sub>3</sub>. Once the XOR is done a ROR by one bit is applied to R<sub>2</sub> and the left and right sides are swaped around so that the next round may begin. The encryption function is applied 16 times before a final swap to undo the swap of the final round and the output is whitened using the subkeys K<sub>4-7</sub>. The resulting 4, 32 bit words are then recombined and returned as the encrypted block.

### Decryption ###

In order to perform decryption the process is run in reverse. The diagram for this is shown below

<p align="center">
<image src = './Twofish_decryption_structure_V1.png' width="600px;"></image>
</p>

### Sources ###

* https://en.wikipedia.org/wiki/Twofish
* https://www.schneier.com/academic/archives/1998/12/the_twofish_encrypti.html
* https://www.schneier.com/academic/paperfiles/paper-twofish-paper.pdf
* https://en.wikipedia.org/wiki/Finite_field
* http://www.cs.cas.cz/portal/AlgoMath/NumberTheory/Arithmetics/Multiplication/PeasantMultiplication.htm
* https://sites.math.washington.edu/~morrow/336_12/papers/juan.pdf