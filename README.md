## Workflow of the Custom Convolution Filter Script

1. **Import Necessary Libraries**

   ```python
   import cv2
   import numpy as np
   from scipy import signal
   from matplotlib import pyplot as plt
   ```

   * **cv2**: OpenCV for image I/O and color conversion
   * **numpy**: Array and matrix operations
   * **scipy.signal**: Convolution function
   * **matplotlib.pyplot**: Displaying images side by side

2. **Specify the Input Images**

   ```python
   image_paths = [
       r'Foto\1.jpg',
       r'Foto\2.jpg',
       r'Foto\3.jpg'
   ]
   ```

   * A list of three file paths pointing to your source images.

3. **Define the Convolution Kernel**

   ```python
   w_k = np.array([
       [0, 0, 0],
       [0, 1, 0],
       [0, 0, 0]
   ], dtype='float')
   ```

   * This kernel is essentially an identity mask (passes the center pixel unchanged).

4. **Rotate the Kernel (180°)**

   ```python
   w_k = np.rot90(w_k, 2)
   ```

   * Convolution mathematically uses a flipped kernel; rotating 180° prepares it correctly.

5. **Prepare the Matplotlib Canvas**

   ```python
   fig, axs = plt.subplots(
       nrows=3, ncols=2,
       figsize=(12, 12)
   )
   ```

   * Creates a grid with 3 rows (one per image) and 2 columns (original vs. filtered).

6. **Process Each Image**

   ```python
   for i, path in enumerate(image_paths):
       # a) Load and convert to grayscale
       img_color = cv2.imread(path)
       img = cv2.cvtColor(img_color, cv2.COLOR_BGR2GRAY)

       # b) Apply 2D convolution (valid mode trims borders)
       filtered = signal.convolve2d(img, w_k, mode='valid')

       # c) Display original
       axs[i, 0].imshow(img, cmap='gray')
       axs[i, 0].set_title(f'Original Image {i+1}', color='white')
       axs[i, 0].axis('off')

       # d) Display filtered
       axs[i, 1].imshow(filtered, cmap='gray')
       axs[i, 1].set_title(f'Filtered Image {i+1}', color='white')
       axs[i, 1].axis('off')
   ```

7. **Adjust Layout and Appearance**

   ```python
   plt.subplots_adjust(wspace=0.2, hspace=0.3)   # Spacing between subplots
   fig.patch.set_facecolor('black')               # Black background for contrast
   plt.tight_layout()
   plt.show()                                     # Render everything
   ```

---

### How It Works

* **Reading & Grayscaling**
  Each image is loaded in color, then converted to a single-channel grayscale image so the filter works on intensity values only.

* **Kernel Preparation**
  The 3×3 matrix with a “1” in the center passes the original pixel value through unchanged. Rotating it by 180° aligns it for proper convolution.

* **Convolution (`valid` mode)**
  `signal.convolve2d(img, w_k, 'valid')` slides the kernel over the image, computing the weighted sum. In `valid` mode, border pixels where the kernel would overflow are omitted, so the output is slightly smaller.

* **Visualization**
  Matplotlib displays each original/filtered pair side by side. Adjusting subplot spacing and using a black figure background enhances visibility.
