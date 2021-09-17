FROM rootproject/root:6.22.08-ubuntu20.04

USER root

RUN apt-get update

RUN apt-get install -y --no-install-recommends \
wget ca-certificates python3 gfortran build-essential ghostscript vim libboost-all-dev \
python3-pip

WORKDIR /home/hep

ENV MG_VERSION="MG5_aMC_v3_2_0"

ENV ROOTSYS /usr/local
ENV PATH $PATH:$ROOTSYS/bin
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$ROOTSYS/lib

RUN pip3 install six

RUN wget --quiet -O- https://launchpad.net/mg5amcnlo/3.0/3.2.x/+download/MG5_aMC_v3.2.0.tar.gz | tar xzf -
WORKDIR /home/hep/${MG_VERSION}

# disable autoupdate
RUN rm /home/hep/${MG_VERSION}/input/.autoupdate

RUN echo install Delphes | /home/hep/${MG_VERSION}/bin/mg5_aMC
RUN echo install ExRootAnalysis | /home/hep/${MG_VERSION}/bin/mg5_aMC

# What a dirty hack...
# madgraph is broken badly
RUN echo install zlib --force --keep_source | /home/hep/${MG_VERSION}/bin/mg5_aMC; exit 0
RUN cd /home/hep/${MG_VERSION}/HEPTools/zlib_src/zlib-1.2.10; ./configure --prefix=/home/hep/${MG_VERSION}/HEPTools/zlib/; make; make install
RUN apt-get install -y --no-install-recommends rsync
RUN printf 'install pythia8\ny\n' | /home/hep/${MG_VERSION}/bin/mg5_aMC

WORKDIR /home/hep/${MG_VERSION}/HEPTools/lhapdf6/share/LHAPDF
# Download a default PDF
RUN wget --quiet -O- https://lhapdf.hepforge.org/downloads?f=pdfsets/v6.backup/6.2.1/NNPDF23_lo_as_0130_qed.tar.gz | tar xzf -

WORKDIR /var/MG_outputs
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/home/hep/${MG_VERSION}/HEPTools/lib/

RUN chmod 777 /home/hep/${MG_VERSION}/models

ADD entry.sh /home/hep/entry.sh
CMD /home/hep/entry.sh
