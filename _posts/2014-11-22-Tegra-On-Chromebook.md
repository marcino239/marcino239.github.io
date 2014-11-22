---
layout: post
title: Cuda and Acer Chromebook 13
---

Here's how to use Tegra K1 on Acer Chromebook 13.
-----

Tegra finally arrived to the chromebook world!  The TK1 chip gives really cool possibilites with 192 Cuda and 4+1 ARM cores.  This post 
will show how to install chrubuntu on the Acer Chromebook 13.  

###Steps:
1. Make sure chromebook is in the developer mode, good instructions are on the [crouton's](https://github.com/dnschneid/crouton).  I recommend setting up chrubuntu on the external SD card.  Acer's formfactor hides SD card pretty well
as opposed to Samsung Chromebook 3
2. Download chrubuntu-tegra.sh from [this repository](https://github.com/marcino239/chrubuntu-tegra) and run from Chrome shell as root.  Original author is arm000 from [reddit](http://www.reddit.com/r/chrubuntu/comments/2hhb31/chrubuntu_on_acer_chromebook_13/)

		sudo bash chrubuntu-tegra.sh default 14.04 /dev/mmcblk1

3. Login with user and password user
4. Download cuda

		wget http://developer.download.nvidia.com/compute/cuda/6_5/rel/installers/cuda-repo-l4t-r21.1-6-5-prod_6.5-14_armhf.deb
		sudo dpkg -i cuda-repo-l4t-r21.1-6-5-prod_6.5-14_armhf.deb
		cd /var/cuda-repo-6-5-prod
		sudo dpkg -i cuda*

5. Let's test cuda

		cd ~
		cuda-install-samples-6.5.sh
		cd NVIDIA_CUDA-6.5_Samples/1_Utilities/deviceQuery
		make
		./deviceQuery
		./deviceQuery Starting

		 CUDA Device Query (Runtime API) version (CUDART static linking)

		Detected 1 CUDA Capable device(s)

		Device 0: "GK20A"
		  CUDA Driver Version / Runtime Version          6.5 / 6.5
		  CUDA Capability Major/Minor version number:    3.2
		  Total amount of global memory:                 4017 MBytes (4212305920 bytes)
		  (1) Multiprocessors, (192) CUDA Cores/MP:     192 CUDA Cores
		  GPU Clock rate:                                72 MHz (0.07 GHz)
		  Memory Clock rate:                             13 Mhz
		  Memory Bus Width:                              64-bit
		  L2 Cache Size:                                 131072 bytes
		  Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65536), 3D=(4096, 4096, 4096)
		  Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers
		  Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers
		  Total amount of constant memory:               65536 bytes
		  Total amount of shared memory per block:       49152 bytes
		  Total number of registers available per block: 32768
		  Warp size:                                     32
		  Maximum number of threads per multiprocessor:  2048
		  Maximum number of threads per block:           1024
		  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
		  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
		  Maximum memory pitch:                          2147483647 bytes
		  Texture alignment:                             512 bytes
		  Concurrent copy and kernel execution:          Yes with 1 copy engine(s)
		  Run time limit on kernels:                     No
		  Integrated GPU sharing Host Memory:            Yes
		  Support host page-locked memory mapping:       Yes
		  Alignment requirement for Surfaces:            Yes
		  Device has ECC support:                        Disabled
		  Device supports Unified Addressing (UVA):      Yes
		  Device PCI Bus ID / PCI location ID:           0 / 0
		  Compute Mode:
			 < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

		deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 6.5, CUDA Runtime Version = 6.5, NumDevs = 1, Device0 = GK20A
		Result = PASS

6. Let's run matrix multiplication using BLAS

		cd ~/NVIDIA_CUDA-6.5_Samples/0_Simple/matrixMulCUBLAS
		make
		./matrixMulCUBLAS 
		Matrix Multiply CUBLAS] - Starting...
		GPU Device 0: "GK20A" with compute capability 3.2

		MatrixA(320,640), MatrixB(320,640), MatrixC(320,640)
		Computing result using CUBLAS...done.
		Performance= 99.09 GFlop/s, Time= 1.323 msec, Size= 131072000 Ops
		Computing result using host CPU...done.
		Comparing CUBLAS Matrix Multiply with CPU results: PASS

7. Here's performance test of opencv using cpu and gpu

		opencv-2.4.9/samples/gpu$ ./performance/test 
		Device 0:  "GK20A"  4017Mb, sm_32, Driver/Runtime ver.6.50/6.50

		Note: the timings for GPU don't include data transfer

			CPU, ms   GPU, ms   SPEEDUP       DESCRIPTION

		matchTemplate:
			858.114   72.0609   x11.9         3000x3000, 32FC1, templ 5x5, CCORR
			985.549   255.025   x3.86         3000x3000, 32FC1, templ 25x25, CCORR
			1748.13   789.76    x2.21         3000x3000, 32FC1, templ 125x125, CCORR

		minMaxLoc:
			19.315    20.6993   x0.933        2000x2000, 32F
			77.0511   15.5263   x4.96         4000x4000, 32F
			307.729   72.4855   x4.25         8000x8000, 32F

		remap:
			90.363    7.50256   x12           1000x1000, 8UC4, INTER_LINEAR, BORDER_REPLICATE
			366.899   17.3507   x21.1         2000x2000, 8UC4, INTER_LINEAR, BORDER_REPLICATE
			1473.39   53.4025   x27.6         4000x4000, 8UC4, INTER_LINEAR, BORDER_REPLICATE

		dft:
			108.717   78.3432   x1.39         1000x1000, 32FC2, complex-to-complex
			484.56    400.052   x1.21         2000x2000, 32FC2, complex-to-complex
			2134.01   1620.15   x1.32         4000x4000, 32FC2, complex-to-complex

		cornerHarris:
			108.305   103.327   x1.05         1000x1000, 32FC1, BORDER_REFLECT101
			450.851   496.52    x0.908        2000x2000, 32FC1, BORDER_REFLECT101
			1881.31   1641.21   x1.15         4000x4000, 32FC1, BORDER_REFLECT101

		integral:
			2.96307   5.38107   x0.551        1000x1000, 8UC1
			9.89759   16.2014   x0.611        2000x2000, 8UC1
			38.8796   747.072   x0.052        4000x4000, 8UC1

		norm:
			66.5294   10.6661   x6.24         2000x2000, 32FC4, NORM_INF
			148.849   143.465   x1.04         3000x3000, 32FC4, NORM_INF
			265.1     83.408    x3.18         4000x4000, 32FC4, NORM_INF

		meanShift:
			898.304   68.3235   x13.1         400x400, 8UC3 vs 8UC4
			3665.37   296.622   x12.4         800x800, 8UC3 vs 8UC4

		SURF:
			11462.1   1404.94   x8.16         

		FAST:
			44.5021   26.4897   x1.68         

		ORB:
			395.996   133.94    x2.96         

		BruteForceMatcher:
			1499.82   80.2282   x18.7         match
			1498.06   79.4344   x18.9         knnMatch
			1562.1    117.872   x13.3         radiusMatch

		magnitude:
			31.7564   10.762    x2.95         2000x2000, 32FC1
			71.0263   35.5927   x2            3000x3000, 32FC1
			125.714   128.134   x0.981        4000x4000, 32FC1

		add:
			18.707    7.99203   x2.34         2000x2000, 32FC1
			42.7588   11.0964   x3.85         3000x3000, 32FC1
			74.9048   133.128   x0.563        4000x4000, 32FC1

		log:
			74.6291   4.42015   x16.9         2000x2000, 32F
			167.232   8.72713   x19.2         3000x3000, 32F
			297.186   14.2314   x20.9         4000x4000, 32F

		mulSpectrums:
			40.4223   9.7163    x4.16         2000x2000
			90.7949   21.1238   x4.3          3000x3000
			169.98    36.9861   x4.6          4000x4000

		resize:
			106.637   11.4671   x9.3          1000x1000, 8UC4, up
			425.265   32.8241   x13           2000x2000, 8UC4, up
			916.219   71.8048   x12.8         3000x3000, 8UC4, up
			3.19822   1.97371   x1.62         1000x1000, 8UC4, down
			11.768    6.89636   x1.71         2000x2000, 8UC4, down
			26.8637   8.29564   x3.24         3000x3000, 8UC4, down

		cvtColor:
			41.3608   9.47918   x4.36         4000x4000, 8UC1, CV_GRAY2BGRA
			256.231   16.2589   x15.8         4000x4000, 8UC3 vs 8UC4, CV_BGR2YCrCb
			305.405   15.5065   x19.7         4000x4000, 8UC4, CV_YCrCb2BGR
			243.121   17.6773   x13.8         4000x4000, 8UC3 vs 8UC4, CV_BGR2XYZ
			262.035   15.625    x16.8         4000x4000, 8UC4, CV_XYZ2BGR
			316.473   39.6782   x7.98         4000x4000, 8UC3 vs 8UC4, CV_BGR2HSV
			1482.77   22.8313   x64.9         4000x4000, 8UC4, CV_HSV2BGR

		erode:
			113.027   30.9059   x3.66         2000x2000
			258.124   60.2367   x4.29         3000x3000
			466.415   236.753   x1.97         4000x4000

		threshold:
			4.94594   2.41176   x2.05         2000x2000, 8UC1, THRESH_BINARY
			11.0253   6.11446   x1.8          3000x3000, 8UC1, THRESH_BINARY
			19.598    8.82527   x2.22         4000x4000, 8UC1, THRESH_BINARY
			10.016    3.97195   x2.52         2000x2000, 32FC1, THRESH_TRUNC [NPP]
			22.4392   31.2924   x0.717        3000x3000, 32FC1, THRESH_TRUNC [NPP]
			40.0042   26.4149   x1.51         4000x4000, 32FC1, THRESH_TRUNC [NPP]

		pow:
			11.9931   2.18587   x5.49         1000x1000, 32F
			47.2433   3.71193   x12.7         2000x2000, 32F
			105.984   7.8099    x13.6         3000x3000, 32F
			189.018   13.3482   x14.2         4000x4000, 32F

		projectPoints:
			148.576   15.1322   x9.82         1000000
			108.388   10.4727   x10.3         714285
			76.3413   10.1459   x7.52         510203
			55.0224   7.87239   x6.99         364430
			38.8461   6.65774   x5.83         260307

		solvePnPRansac:
			554.475   343.844   x1.61         5000
			1292.95   399.338   x3.24         18800
			5010.72   627.099   x7.99         70688
			17729.6   1454.83   x12.2         265786

		GaussianBlur:
			36.0077   16.7083   x2.16         1000x1000, 8UC4
			146.934   29.5295   x4.98         2000x2000, 8UC4
			333.92    58.5911   x5.7          3000x3000, 8UC4
			601.25    197.405   x3.05         4000x4000, 8UC4

		filter2D:
			41.9738   13.9314   x3.01         ksize = 3, 512x512, 8UC4
			72.3195   17.1541   x4.22         ksize = 5, 512x512, 8UC4
			115.287   20.4971   x5.62         ksize = 7, 512x512, 8UC4
			181.174   25.6706   x7.06         ksize = 9, 512x512, 8UC4
			181.107   34.2712   x5.28         ksize = 11, 512x512, 8UC4
			181.266   46.1384   x3.93         ksize = 13, 512x512, 8UC4
			181.523   58.7195   x3.09         ksize = 15, 512x512, 8UC4
			168.459   19.6847   x8.56         ksize = 3, 1024x1024, 8UC4
			290.151   32.4336   x8.95         ksize = 5, 1024x1024, 8UC4
			461.43    52.3748   x8.81         ksize = 7, 1024x1024, 8UC4
			566.945   81.0616   x6.99         ksize = 9, 1024x1024, 8UC4
			567.505   115.59    x4.91         ksize = 11, 1024x1024, 8UC4
			568.561   183.934   x3.09         ksize = 13, 1024x1024, 8UC4
			570.085   237.825   x2.4          ksize = 15, 1024x1024, 8UC4
			675.046   49.528    x13.6         ksize = 3, 2048x2048, 8UC4
			1170.26   110.083   x10.6         ksize = 5, 2048x2048, 8UC4
			1859.11   196.855   x9.44         ksize = 7, 2048x2048, 8UC4
			1999.81   395.12    x5.06         ksize = 9, 2048x2048, 8UC4
			1994.11   518.987   x3.84         ksize = 11, 2048x2048, 8UC4
			2032.05   729.437   x2.79         ksize = 13, 2048x2048, 8UC4
			2005.78   977.641   x2.05         ksize = 15, 2048x2048, 8UC4

		pyrDown:
			235.279   39.2804   x5.99         4000x4000, 8UC4
			130.707   40.4058   x3.23         3000x3000, 8UC4
			57.1578   13.7517   x4.16         2000x2000, 8UC4
			13.6359   8.41556   x1.62         1000x1000, 8UC4

		pyrUp:
			263.64    89.4818   x2.95         2000x2000, 8UC4
			65.418    16.3824   x3.99         1000x1000, 8UC4

		equalizeHist:
			3.23604   8.55969   x0.378        1000x1000
			11.3514   20.3959   x0.557        2000x2000
			25.293    36.5682   x0.692        3000x3000

		Canny:
			61.9438   47.8773   x1.29         

		reduce:
			3.04195   1.15759   x2.63         1000x1000, dim = 0
			2.69111   2.31457   x1.16         1000x1000, dim = 1
			9.98761   3.39094   x2.95         2000x2000, dim = 0
			9.66781   3.91534   x2.47         2000x2000, dim = 1
			21.2211   8.76042   x2.42         3000x3000, dim = 0
			20.7015   5.4969    x3.77         3000x3000, dim = 1

		gemm:
			[error: The library was build without CUBLAS] 512x512

		GoodFeaturesToTrack:
			188.499   40.3744   x4.67         

		PyrLKOpticalFlow:
			312.542   151.67    x2.06         1000
			614.213   420.687   x1.46         2000
			1177.81   697.847   x1.69         4000
			1216.7    611.392   x1.99         8000

		FarnebackOpticalFlow:
			641.565   611.623   x1.05         dataset=rubberwhale, fastPyramids=0, useGaussianBlur=0
			1182.49   363.342   x3.25         dataset=rubberwhale, fastPyramids=0, useGaussianBlur=1
			640.733   170.038   x3.77         dataset=rubberwhale, fastPyramids=1, useGaussianBlur=0
			1180.53   173.912   x6.79         dataset=rubberwhale, fastPyramids=1, useGaussianBlur=1
			893.146   965.563   x0.925        dataset=basketball, fastPyramids=0, useGaussianBlur=0
			1621.17   623.748   x2.6          dataset=basketball, fastPyramids=0, useGaussianBlur=1
			896.168   747.02    x1.2          dataset=basketball, fastPyramids=1, useGaussianBlur=0
			1655.28   312.789   x5.29         dataset=basketball, fastPyramids=1, useGaussianBlur=1

		FGDStatModel:
			513.208   257.048   x2            

		MOG:
			96.1007   10.7464   x8.94         

		MOG2:
			84.3416   13.3616   x6.31         

		average GPU speedup: x6.240

