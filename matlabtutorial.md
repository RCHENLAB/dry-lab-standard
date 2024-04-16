### How to set Matlab on your local machine and run jobs on Taco server

## Prerequisites


Your Matlab version must be R2017a or later.
You must have the Matlab Parallel Computing Toolbox installed.  To see what toolboxes are installed, type the ver command in the Matlab command window.  You should see Parallel Computing Toolbox listed.
You must be connected to the ISU VPN in order to submit jobs from your desktop, regardless of whether you are on-campus or off-campus. (This is because the Matlab cluster integration tools do not support multi-factor authentication such as Okta.   The VPN connection helps enforce multi-factor authentication.)
You must ssh to the cluster at least once before trying to submit any jobs from Matlab.


## Download Matlab from BCM 


      1. Go to our MATLAB Portal provided by Baylor College of Medicine (https://www.mathworks.com/academia/tah-portal/baylor-college-of-medicine-30720220.html) and Select ‘Sign in to get started’ under the Get MATLAB and Simulink section.

      2. Log into your MathWorks account that is associated to your BCM email address.

              a. If you have not already created a Mathworks account you will need to do so using your BCM email address only.

      3. Click the download button for the current release. (Users can also download previous releases here).

      4. Choose a supported platform and download the installer.


## Installation


To install and Activate:

      1. Run the installer.

      2. In the installer, select Log in with a MathWorks Account and follow the online instructions.

      3. When prompted to do so, select the Academic – Total Headcount license labeled Individual.

      4. Select the products you want to download and install.

      5. After downloading and installing your products, keep the Activate MATLAB checkbox selected and click Next.

      6. Follow the prompts to activate MATLAB.



## Connect to Taco server

Download the Cluster Plugin Scripts
In order to submit jobs from your desktop, you will need to install some custom Matlab plugin scripts that know how to interact with the Slurm job scheduler on a cluster.  There is a separate cluster plugin script bundle for each cluster (Nova, Condo, and HPC-class).   Each bundle contains configuration files and scripts packaged together as .zip files (for Windows and Mac) and as .tar.gz files (for Linux).  Use the links below to download the appropriate script bundle you need based on the cluster(s) you plan to use from the links below. 

💻 For Mac users:



Unzip this file [https://github.com/RCHENLAB/dry-lab-standard/blob/main/mhgcp-config-cluster.zip] on your Mac and put it your local MatLab path (for example, "/Users/cristalvillalba/Documents/MATLAB/mhgcp-config-cluster"
Open MatLab and browse to that folder and open the clusterConfig.m file
Follow the instructions under the command window screen

💃 💃 💃voilà💃💃💃


💻 For windows users: 

        **UNDER CONSTRUCTION** 🚧 👷‍♀️👷‍♀️





