# explicitly use Debian for maximum cross-architecture compatibility
FROM ubuntu:18.04

RUN dpkg --add-architecture x86-64


RUN apt-get update && apt-get install -y --no-install-recommends \
		gcc \
		wget \
		libc6-dev \
		dpdk-igb-uio-dkms \
		make \
		linux-gcp \
		numactl libnuma-dev libnuma1 \
		xz-utils \
		\
		file \
	&& rm -rf /var/lib/apt/lists/*

WORKDIR /usr/src/

COPY . .

RUN wget https://fast.dpdk.org/rel/dpdk-18.11.2.tar.xz --no-check-certificate && mkdir dpdk_code && tar -xvf dpdk-18.11.2.tar.xz -C dpdk_code --strip-components 1 && rm -f dpdk-18.11.2.tar.xz && cd dpdk_code && export RTE_TARGET=x86_64-native-linuxapp-gcc && export RTE_SDK=$PWD 

#&& ./app/dpdk-setup.sh &&  cd app/ && make


#WORKDIR /usr/src/dpdk_code/app1
WORKDIR /usr/src/app1/
COPY . /usr/src/dpdk_code/
RUN ls -l
WORKDIR /usr/src/dpdk_code/
#RUN ls -l app1/
RUN ./app1/dpdk-setup.sh &&  cd app1/ && make

CMD ["./build/kni -c 0x3 -n 4 -- -P -p 0x3 --config='(0,1,2)'"]
