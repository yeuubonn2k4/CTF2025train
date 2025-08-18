> Hello everyone, I am lynchee, a member of SECJOTA, a team of Cybersecurity in Posts and Telecommunications Institute of Technology (PTIT), Viet Nam. Above is my writeup for challenge "The Insider4":

## CHALLENGE DESCRIPTION:

- Good luck! Note: max flag limit is 6 for a reason, you should be able to get it in less than that. If not, open a ticket. Flag is case insensitive

## SOLUTION:

- Based on previous challenges, I found NoobMaster999's github:

`
https://github.com/scriptCTF/scriptCTF26/tree/main/OSINT/.insider-4/attachments
`

2 pictures in challenge:

<img width="935" height="702" alt="image" src="https://github.com/user-attachments/assets/8ba40c62-557b-4c38-a1fa-c09e9aa13d36" />

<img width="937" height="703" alt="image" src="https://github.com/user-attachments/assets/689e93a3-4f9f-4e35-81db-16eb7a574915" />

and have a description: `"As a photographer, I took these photos on my vacation. Flag format is scriptCTF{HOTEL_ADDRESS_ROOMNUMBER}. Example: scriptCTF{1337_elite_Hwy_S_9999} Have fun!"`

- In commit ".secret": *as a photographer, i add comments/descriptions to my images*. I thought of using exiftool to see comments/ description in this images and I have the following data after exiftool:

```
ExifTool Version Number         : 12.76
File Name                       : fireworks.jpg
Directory                       : .
File Size                       : 699 kB
File Modification Date/Time     : 2025:08:16 23:47:02+07:00
File Access Date/Time           : 2025:08:16 23:47:18+07:00
File Inode Change Date/Time     : 2025:08:16 23:47:02+07:00
File Permissions                : -rwxrwxrwx
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Byte Order                 : Big-endian (Motorola, MM)
Orientation                     : Horizontal (normal)
Current IPTC Digest             : d41d8cd98f00b204e9800998ecf8427e
IPTC Digest                     : d41d8cd98f00b204e9800998ecf8427e
Profile CMM Type                : Apple Computer Inc.
Profile Version                 : 4.0.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 2022:01:01 00:00:00
Profile File Signature          : acsp
Primary Platform                : Apple Computer Inc.
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : Apple Computer Inc.
Device Model                    : 
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Perceptual
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Apple Computer Inc.
Profile ID                      : ecfda38e388547c36db4bd4f7ada182f
Profile Description             : Display P3
Profile Copyright               : Copyright Apple Inc., 2022
Media White Point               : 0.96419 1 0.82489
Red Matrix Column               : 0.51512 0.2412 -0.00105
Green Matrix Column             : 0.29198 0.69225 0.04189
Blue Matrix Column              : 0.1571 0.06657 0.78407
Red Tone Reproduction Curve     : (Binary data 32 bytes, use -b option to extract)
Chromatic Adaptation            : 1.04788 0.02292 -0.0502 0.02959 0.99048 -0.01706 -0.00923 0.01508 0.75168
Blue Tone Reproduction Curve    : (Binary data 32 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 32 bytes, use -b option to extract)
Comment                         : Great fireworks! Thanks to the Wendell family for organizing these!
Image Width                     : 4032
Image Height                    : 3024
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 4032x3024
Megapixels                      : 12.2
```

- Look at the comment line: `Great fireworks! Thanks to the Wendell family for organizing these!`, I found out the fireworks picture was of a fireworks display organized by the Wendell family and I googled "Wendell family organize fireworks":

<img width="929" height="661" alt="image" src="https://github.com/user-attachments/assets/6a0f3269-f6da-4fbe-bab5-5de7846df511" />

- And I see it says fireworks at Little Bay,Rockport, TX 78382, USA.  Use google map to search Little map. After searching for a while, I was able to narrow down the area similar to the second photo thanks to the two houses.

<img width="1258" height="481" alt="image" src="https://github.com/user-attachments/assets/0caa2c62-113d-4b56-800a-4972c85617c5" />

- After seeing these two houses, I determined that I had narrowed down the area correctly and that I had to find a hotel across from these two houses and facing Little Bay. I looked closely at the second photo and found a hotel with a green building and a small flower bed surrounded by 4 wooden chairs. And I found `Days Inn by Wyndham Rockport Texas` has the same building, colors, sign, 4 chairs, bay view as the 2nd photo provided.

<img width="1259" height="816" alt="image" src="https://github.com/user-attachments/assets/da7c2408-0f69-4d26-aab2-e39b6952ad66" />

- I determined that part of the flag is scriptCTF{901_Hwy_35_N according to the format of the problem. Now I need to find the room number where the photographer took the photo. Looking at the second photo, I see that the photo was taken from the middle building of the three buildings. Searching google about this hotel, I found that the hotel has 48 rooms and 2 floors. From the angle, I guess the photo was taken from the 1st floor and in one of the two rooms in the middle of the 1st floor. 2 floors 48 rooms so 1 floor will have about 24 rooms, and 2 middle rooms will be about 11,12.According to the information I searched, hotels are usually numbered from 100 onwards. So I tried entering 111 or 112. Luckily when I filled in `scriptCTF{901_Hwy_35_N_111}` it came out Correctly, I am extremely proud that my mathematical logic thinking was correct in this challenge. Thank you for reading my writeup. 


