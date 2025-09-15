<img width="640" height="360" alt="image" src="https://github.com/user-attachments/assets/a33217f7-04ce-4272-8a57-3c0362a4356d" />

This picture seems oddly familiar… but something about it feels ever so slightly off.

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/8de7a50d-c4d2-4461-8446-01d5552bd92f" />

# Solution:

= Tệp đính kèm cung cấp 1 cái tệp webp. Search google len thì thấy ảnh gốc:
https://fortid.com/about/

= Lưu ảnh về máy ở trang about trên dưới tên `about-us-team.I3TrCs6f_4E8U9.webp`

= Thực hiện phép XOR 2 ảnh:

```
import numpy as np
from PIL import Image, ImageChops, ImageEnhance

# Open images
im1 = Image.open("about-us-team.I3TrCs6f_4E8U9.webp")
im2 = Image.open("about_us.webp")
print(im1, im2)

# Make into Numpy arrays
im1np = np.array(im1) * 255
im2np = np.array(im2) * 255

# XOR with Numpy
result = np.bitwise_xor(im1np, im2np).astype(np.uint8)

Image.fromarray(result).save("result.png")
```

= Để làm rõ cờ, hãy sử dụng ImageEnhance.Contrast:

```
import numpy as np
from PIL import Image, ImageChops, ImageEnhance

# Open images
im1 = Image.open("about-us-team.I3TrCs6f_4E8U9.webp")
im2 = Image.open("about_us.webp")
print(im1, im2)

# Make into Numpy arrays
im1np = np.array(im1) * 255
im2np = np.array(im2) * 255

# XOR with Numpy
result = np.bitwise_xor(im1np, im2np).astype(np.uint8)

img = Image.fromarray(result)
```

<img width="900" height="501" alt="image" src="https://github.com/user-attachments/assets/a88f08c6-7560-4015-b285-50d0a70cdc74" />

=> Flag:
`
FortID{1f_Y0u_W4nna_L3arn_M0r3_Ab0u7_Us_Try_S0lv1n6_051N7_Ex4m}
`


ImageEnhance.Contrast(img).enhance(100).save("result.png")

