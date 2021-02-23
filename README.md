# Install dependencies:

```
sudo apt-get install git cmake clang libboost-dev libtool automake autoconf pkg-config libipt-dev
```

# Install Triton:

```
git submodule update --init triton
cd triton
mkdir build
cd build
cmake ..
sudo make install
cd ../..
```

# Install LibVMI:

```
git submodule update --init libvmi
cd libvmi
autoreconf -vif
./configure --disable-kvm
make
sudo make install
cd ..
```

# Build vmtaint:

```
autoreconf -vif
./configure
make
```

# Collect IPT log:

```
xl pause <domid>
vmtaint --save-state state.log --domid <domid>
timeout -s 2 60 xen-vmtrace <domid> 0 > vmtrace.log &
xl unpause <domid>
```

# Run vmtaint:

```
vmtaint \
    --load-state state.log \
    --pt vmtrace.log \
    --domid <domid> \
    --taint-address <virtual address> \
    --taint-size <taint size> \
    --json <kernel's debug info in json>
```

# Example:

```
./vmtaint --load-state state.log --domid 96 --pt vmtrace.log --json 5.4.0-48.json --taint-address 0xffffffffc0367010 --taint-size 9
ffffffffc0365095        movsx edi, byte ptr [rip + 0x1f74]
         Tainted reg: rdi: 0
ffffffffc036509c        call 0xffffffffc036500b
         Tainted reg: rdi: 0
ffffffffc036500b        nop dword ptr [rax + rax]
         Tainted reg: rdi: 0
ffffffffc0365010        push rbp
         Tainted reg: rdi: 0
ffffffffc0365011        mov rax, qword ptr [rip + 0x1fe8]
         Tainted reg: rdi: 0
ffffffffc0365018        cmp qword ptr [rip + 0x1ff1], rax
         Tainted reg: rdi: 0
ffffffffc036501f        mov rbp, rsp
         Tainted reg: rdi: 0
ffffffffc0365022        jne 0xffffffffc0365032
         Tainted reg: rdi: 0
         Tainted reg: rip: ffffffffc0365024
ffffffffc0365032        mov rdi, -0x3fc99fbc
ffffffffc0365039        call 0xffffffff81114873
ffffffff81114873        nop dword ptr [rax + rax]
```
