ARG MATLAB_RELEASE=r2022b

FROM mathworks/matlab-deps:${MATLAB_RELEASE}

ENV http_proxy 'http://proxy.tch.harvard.edu:3128'
ENV  https_proxy 'http://proxy.tch.harvard.edu:3128'

ENV LANG="en_US.UTF-8" \
    LC_ALL="en_US.UTF-8" \
    ND_ENTRYPOINT="/neurodocker/startup.sh"

USER root

##matlab
ENV PATH="/opt/matlab:$PATH"
RUN export DEBIAN_FRONTEND=noninteractive \
    && apt-get update \
    && apt-get install --no-install-recommends --yes \
    wget \
    unzip \
    ca-certificates \
    && apt-get clean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

RUN wget -q https://www.mathworks.com/mpm/glnxa64/mpm \
    && chmod +x mpm \
    && ./mpm install \
    --release=R2022b \
    --destination=/opt/matlab \
    --products MATLAB \
    || (echo "MPM Installation Failure. See below for more information:" && cat /tmp/mathworks_root.log && false) \
    && rm -f mpm /tmp/mathworks_root.log \
    && ln -s /opt/matlab/bin/matlab /usr/local/bin/matlab

COPY network.lic /opt/matlab/licenses/


##CBIG dependencies
RUN apt-get update -qq \
    && apt-get install -y -q --no-install-recommends \
           apt-utils \
           bzip2 \
           curl \
           git \
           jq \
           python \
           nano \
           gnupg \
           gawk \
           locales



## Set up the entrypoint script $ND_ENTRYPOINT if it doesn't already exist
RUN apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG="en_US.UTF-8" \
    && chmod 777 /opt && chmod a+s /opt \
    && mkdir -p /neurodocker \
    && if [ ! -f "$ND_ENTRYPOINT" ]; then \
         echo '#!/usr/bin/env bash' >> "$ND_ENTRYPOINT" \
    &&   echo 'set -e' >> "$ND_ENTRYPOINT" \
    &&   echo 'export USER="${USER:=`whoami`}"' >> "$ND_ENTRYPOINT" \
    &&   echo 'if [ -n "$1" ]; then "$@"; else /usr/bin/env bash; fi' >> "$ND_ENTRYPOINT"; \
    fi \
    && chmod -R 777 /neurodocker && chmod a+s /neurodocker


##create  directories for input and output data
RUN mkdir -p /BIDS_input \
    && mkdir -p /freesurfer_input \
    && mkdir -p /output \
    && mkdir -p /extra_files

RUN wget http://archive.ubuntu.com/ubuntu/pool/main/g/glibc/multiarch-support_2.27-3ubuntu1.6_amd64.deb \
    &&  dpkg -i multiarch-support_2.27-3ubuntu1.6_amd64.deb

##install ANTS 2.20
ENV ANTSPATH="/opt/ants-2.2.0" \
    PATH="/opt/ants-2.2.0:$PATH"

RUN mkdir -p /opt/ants-2.2.0 \
    && curl -fsSL --retry 5 https://dl.dropbox.com/s/2f4sui1z6lcgyek/ANTs-Linux-centos5_x86_64-v2.2.0-0740f91.tar.gz \
    | tar -xz -C /opt/ants-2.2.0 --strip-components 1



ENV PATH="/opt/afni-latest:$PATH" \
    AFNI_PLUGINPATH="/opt/afni-latest"
RUN apt-get update -qq \
    && apt-get install -y -q --no-install-recommends \
           ed \
           gsl-bin \
           libglib2.0-0 \
           libglu1-mesa-dev \
           libglw1-mesa \
           libgomp1 \
           libjpeg62 \
           libxm4 \
           netpbm \
           tcsh \
           xfonts-base \
           xvfb \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && wget http://ftp.debian.org/debian/pool/main/libx/libxp/libxp6_1.0.2-2_amd64.deb \
    && dpkg -i libxp6_1.0.2-2_amd64.deb \
    && rm libxp6_1.0.2-2_amd64.deb \
    && curl -sSL --retry 5 -o /tmp/toinstall.deb http://snapshot.debian.org/archive/debian-security/20160113T213056Z/pool/updates/main/libp/libpng/libpng12-0_1.2.49-1%2Bdeb7u2_amd64.deb \
    && dpkg -i /tmp/toinstall.deb \
    && rm /tmp/toinstall.deb \
    && apt-get install -f \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && gsl2_path="$(find / -name 'libgsl.so.19' || printf '')" \
    && if [ -n "$gsl2_path" ]; then \
         ln -sfv "$gsl2_path" "$(dirname $gsl2_path)/libgsl.so.0"; \
    fi \
    && ldconfig \
    && echo "Downloading AFNI ..." \
    && mkdir -p /opt/afni-latest \
    && curl -fsSL --retry 5 http://afni.nimh.nih.gov/pub/dist/tgz/linux_openmp_64.tgz \
    | tar -xz -C /opt/afni-latest --strip-components 1

##install FSL 5.0.10
ENV FSLDIR="/opt/fsl-5.0.10" \
    PATH="/opt/fsl-5.0.10/bin:$PATH" \
    FSLOUTPUTTYPE="NIFTI_GZ" \
    FSLMULTIFILEQUIT="TRUE" \
    FSLTCLSH="/opt/fsl-5.0.10/bin/fsltclsh" \
    FSLWISH="/opt/fsl-5.0.10/bin/fslwish" \
    FSLLOCKDIR="" \
    FSLMACHINELIST="" \
    FSLREMOTECALL="" \
    FSLGECUDAQ="cuda.q"

RUN apt-get update -qq \
    && apt-get install -y -q --no-install-recommends \
           bc \
           dc \
           file \
           libfontconfig1 \
           libfreetype6 \
           libgl1-mesa-dev \
           libgl1-mesa-dri \
           libice6 \
           libxcursor1 \
           libxft2 \
           libxinerama1 \
           libxrandr2 \
           libxrender1 \
           libxt6 \
           sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && echo "Downloading FSL ..." \
    && mkdir -p /opt/fsl-5.0.10 \
    && curl -fsSL --retry 5 https://fsl.fmrib.ox.ac.uk/fsldownloads/fsl-5.0.10-centos6_64.tar.gz \
    | tar -xz -C /opt/fsl-5.0.10 --strip-components 1 \
    && sed -i '$iecho Some packages in this Docker container are non-free' $ND_ENTRYPOINT \
    && sed -i '$iecho If you are considering commercial use of this container, please consult the relevant license:' $ND_ENTRYPOINT \
    && sed -i '$iecho https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/Licence' $ND_ENTRYPOINT \
    && sed -i '$isource $FSLDIR/etc/fslconf/fsl.sh' $ND_ENTRYPOINT \
    && echo "Installing FSL conda environment ..." \
    && bash /opt/fsl-5.0.10/etc/fslconf/fslpython_install.sh -f /opt/fsl-5.0.10


##Install FreeSurfer 6.0.0
ENV FREESURFER_HOME="/opt/freesurfer-6.0.0" \
    PATH="/opt/freesurfer-6.0.0/bin:$PATH"
RUN apt-get update -qq \
    && apt-get install -y -q --no-install-recommends \
           libxmu6 \
           perl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && echo "Downloading FreeSurfer ..." \
    && mkdir -p /opt/freesurfer-6.0.0 \
    && curl -fsSL --retry 5 ftp://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/6.0.0/freesurfer-Linux-centos6_x86_64-stable-pub-v6.0.0.tar.gz \
    | tar -xz -C /opt/freesurfer-6.0.0 --strip-components 1 \
         --exclude='freesurfer/average/mult-comp-cor' \
         --exclude='freesurfer/lib/cuda' \
         --exclude='freesurfer/lib/qt' \
         --exclude='freesurfer/subjects/V1_average' \
         --exclude='freesurfer/subjects/bert' \
         --exclude='freesurfer/subjects/cvs_avg35' \
         --exclude='freesurfer/subjects/cvs_avg35_inMNI152' \
         --exclude='freesurfer/subjects/fsaverage3' \
         --exclude='freesurfer/subjects/fsaverage4' \
         --exclude='freesurfer/subjects/fsaverage_sym' \
         --exclude='freesurfer/trctrain' \
    && sed -i '$isource "/opt/freesurfer-6.0.0/SetUpFreeSurfer.sh"' "$ND_ENTRYPOINT" \
    && mkdir root/matlab && touch root/matlab/startup.m

##install Connectome Workbench
RUN echo "Downloading Connectome Workbench" \
    && wget -q -P /opt/ https://www.humanconnectome.org/storage/app/media/workbench/workbench-linux64-v1.5.0.zip \
    && cd /opt \
    && unzip -q workbench-linux64-v1.5.0.zip \
    && rm workbench-linux64-v1.5.0.zip \
    && cd \
    && apt-get update -qq \
    && apt-get install -y -q --no-install-recommends connectome-workbench \
       libqt5core5a \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV LD_LIBRARY_PATH="/usr/lib/x86_64-linux-gnu:${LD_LIBRARY_PATH}"

RUN apt-get update && \
    apt-get install -y binutils
RUN strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.so.5

##BIDS_to_CBIG_fMRI_Preproc2016
RUN mkdir -p /repos \
    && git clone https://github.com/ThomasYeoLab/CBIG.git \
    && git clone https://github.com/bchcohenlab/BIDS_to_CBIG_fMRI_Preproc2016.git



RUN chmod +x /BIDS_to_CBIG_fMRI_Preproc2016/BIDS_2BP_Thomas_docker_CBIG_fMRI_preprocess.sh \
    && chmod +x /BIDS_to_CBIG_fMRI_Preproc2016/Thomas_CBIG_2BP_docker_config.sh   \
    && sed -i '$isource /BIDS_to_CBIG_fMRI_Preproc2016/Thomas_CBIG_2BP_docker_config.sh' $ND_ENTRYPOINT


COPY license.txt /opt/freesurfer-6.0.0/license.txt

COPY CBIG_preproc_native2mni.csh CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_native2mni.csh 
RUN  chmod +x /CBIG/stable_projects/preprocessing/CBIG_fMRI_Preproc2016/CBIG_preproc_native2mni.csh   

COPY SubcortCerebellumWhiteMask.GCA.t0.5_resampled.nii.gz /CBIG/data/templates/volume/FS_nonlinear_volumetric_space_4.5/SubcortCerebellumWhiteMask.GCA.t0.5_resampled.nii.gz
RUN  chmod 777 /CBIG/data/templates/volume/FS_nonlinear_volumetric_space_4.5/SubcortCerebellumWhiteMask.GCA.t0.5_resampled.nii.gz


RUN apt-get update && \
    apt-get install -y bash && \
    apt`-get install -y rsync 


ENTRYPOINT ["/neurodocker/startup.sh"]
