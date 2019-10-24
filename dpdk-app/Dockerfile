# explicitly use Debian for maximum cross-architecture compatibility
FROM ubuntu:18.04

RUN dpkg --add-architecture x86-64


RUN apt-get update && apt-get upgrade -y && apt-get install -y --no-install-recommends \
		gcc \
		wget \
		libc6-dev \
		make \
		linux-gcp \
		numactl libnuma-dev libnuma1 \
		xz-utils \
		dpdk \
		dpdk-dev \
		dpdk-igb-uio-dkms\
		dpdk-rte-kni-dkms\
		libdpdk-dev \
		libarchive-dev \
		build-essential \
		linux-headers-$(uname -r) \
		\
		file \
	&& rm -rf /var/lib/apt/lists/*


WORKDIR /usr/src/
COPY . .

RUN wget https://fast.dpdk.org/rel/dpdk-18.11.2.tar.xz --no-check-certificate && mkdir dpdk_code && tar -xvf dpdk-18.11.2.tar.xz -C dpdk_code --strip-components 1 && rm -f dpdk-18.11.2.tar.xz && cd dpdk_code && export RTE_TARGET=x86_64-native-linuxapp-gcc && export RTE_SDK=$PWD 


WORKDIR /usr/src/app1/
COPY . /usr/src/dpdk_code/
RUN ls -l
WORKDIR /usr/src/dpdk_code/

RUN cp setup_config.sh config/
WORKDIR /usr/src/dpdk_code/config
RUN ./setup_config.sh 
WORKDIR /usr/src/dpdk_code

#COMPILE THE DPDK CODE
RUN ./app1/dpdk-setup.sh 
#CMD ["./app1/dpdk-setup.sh"]
#COMPILE THE APPLICATION CODE
RUN export RTE_SDK=/usr/src/dpdk_code/&& cd app1/ && make

#RUN rmmod rte_kni.ko && insmod insmod rte_kni.ko "lo_mode=lo_mode_ring"
RUN /sbin/insmod ./x86_64-default-linuxapp-gcc/kmod/rte_kni.ko "lo_mode=lo_mode_ring"

#RUN THE APPLICATION
#CMD ["export RTE_SDK=$PWD"]
#CMD ["./build/kni -c 0x3 -n 4 -- -P -p 0x3 --config='(0,1,2)'"]
CMD ["./build/kni -c 0x1 -n 4 -- -P -p 0x1 --config='(0,1,1)'"]
