************
Installation
************

Installing the latest version
=============================

Conda
-----
It is highly recommended Dolphot-LC and its dependencies be installed through
Anaconda using the Astroconda software stack. To add the Astroconda channel to
an Anaconda installation enter the following via a command line. ::

    conda config --add channels http://ssb.stsci.edu/astroconda

Create and activate an Anaconda environment. ::

    conda create -n <env name> python=3.7 stsci

    conda activate <env name>

After the Anaconda environment has been activated, Source-Extractor can then
be installed. ::

    conda install -c conda-forge astromatic-source-extractor

Finally, install Dolphot-LC. ::

    pip install dolphot-lc

Testing an installed Dolphot-LC Package
=======================================
Clone code from Dolphot-LC Github and enter exmaple script directory ::

    git clone https://github.com/patkel/dolphot_lc.git

    cd dolphot_lc/example

Then, download the needed dolphot scripts and data files. ::

    for f in tar.gz ACS.tar.gz WFC3.tar.gz WFPC2.tar.gz Roman.tar.gz; do wget http://americano.dolphinsim.com/dolphot/dolphot2.0."$f"; done

    for f in ACS_WFC_PAM ACS_WFC_F814W WFC3_UVIS_PAM WFC3_UVIS_F814W; do wget http://americano.dolphinsim.com/dolphot/"$f".tar.gz; done

Extract files and enter the dolphot2.0 directory. ::

    for f in *.tar.gz; do tar xf "$f"; done

    cd dolphot2.0

Edit the Makefile to enable the ACS and WFC3 modules. ::

    line 20
    #export USEACS=1
    export USEACS=1

    line 23
    #export USEWFC3=1
    export USEWFC3=1

Edit dolphot_common.h to allow for measurement of negative counts. ::

    line 345
    if (!strcasecmp(var,"SigFind")) {SigFind=x; if (SigFind<=0) perr("SigFind>0"); return 1;}
    if (!strcasecmp(var,"SigFind")) {SigFind=x; if (SigFind<=-999) perr("SigFind>0"); return 1;}

    line 347
    if (!strcasecmp(var,"SigFinal")) {SigFinal=x; if (SigFinal<=0) perr("SigFinal>0"); return 1;}
    if (!strcasecmp(var,"SigFinal")) {SigFinal=x; if (SigFinal<=-999) perr("SigFinal>0"); return 1;}

Edit dolphot.c to allow for measurement of negative counts. ::

    line 5270
    if (i<0 || (WARMSTART!=2 && (stars[i].s<=0 || stars[i].s<SigFinal*stars[i].ss)) || stars[i].x<XMIN2 || stars[i].x>=XMAX2 || stars[i].y<YMIN2 || stars[i].y>=YMAX2) {
    if (i<0 || (WARMSTART!=2 && (stars[i].s<=-9999 || stars[i].s<SigFinal*stars[i].ss)) || stars[i].x<XMIN2 || stars[i].x>=XMAX2 || stars[i].y<YMIN2 || stars[i].y>=YMAX2) {

Compile dolphot2.0 software. ::

    make


Exit dolphot2.0 directory and enter example script directory. ::

    cd ../example

The following scripts will download the necessary data from the Mikulski Archive 
for Space Telescopes (MAST).

Create registration image. ::

    python3 make_registration.py

Run example image analysis pipeline. ::

    python3 dolphot_lc_test.py
