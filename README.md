# CBC_Container_Cat_Example
This repository is intended to highlight, and simplify, the process for building, and running, an demo example comprised of multiple varying k8 deployments. This demo is intended to showcase one of the many capabilities of the CBC Container Security offering; configuration controls for optimal k8 hygiene. 

## Setup Instructions
1. Download server ISO from Ubuntu's [page.](https://releases.ubuntu.com/20.04/)
    * Be certain to download one with a GUI, for demo purposes
    
2. Create the VM.
    * Do not use 'Easy Install' option
    * I suggest 4GB of RAM, 2 CPU cores, and 40GB storage/HD
    * Ideally VMware Tools is installed
    
3. _(Optional)_ Once VM is created, select ‘Activities’ bar on the upper left-hand corner.
    * In the search window; type ‘Terminal’
      * Drag and drop over to your ‘Favorites’ in the left-hand navigation panel
    * In the search window; type ‘Power’
      * Select the ‘Power’ option under ‘Settings’
      * Adjust ‘Blank Screen’ drop down to either ‘15 Minutes’ or ‘Never’
      
4. Download, or git, the [k8_demo_setup.sh]() listed above, and move to your Ubuntu VM.
    * NOTE: If you copy and paste you might run into errors with special characters being added to the script
    
5. Open terminal window on your Ubuntu Machine
    * Run 'sudo -i' and input in your user password to achieve root access
    
6. Navigate to the location of the script you downloaded/copied above.

7. Run the script with the following command: _sh k8_demo_setup.sh_

8. Upon completion of the script
    
      
      

    
