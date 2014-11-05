start_kernel
------------

### boot_init_stack_canary
|    | \# TO tag | FROM | line | in file/text |
|-----|:-----------:|------|------|--------------|
| 1  | 2  | boot_init_stack_canary |   485  |  init/main.c |
| 2  | 1  | get_random_bytes       |   73   | arch/x86/include/asm/stackprotector.h |
| 3  | 1  | extract_entropy        |   1063 |   drivers/char/random.c |
| 4  | 1  | xfer_secondary_pool    |   990  |  drivers/char/random.c |
| 5  | 1  | credit_entropy_bits    |   860  |  drivers/char/random.c |
| 6  | 1  | kill_fasync            |   607  |  drivers/char/random.c |
| 7  | 1  | kill_fasync_rcu        |   825  |  fs/fcntl.c |
| 8  | 1  | send_sigio             |   811  |  fs/fcntl.c |
| 9  | 1  | send_sigio_to_task     |   606  |  fs/fcntl.c |
| 10 |  1 |  sigio_perm            |   553  |  fs/fcntl.c |
| 11 |  1 |  rcu_read_lock         |   533  |  fs/fcntl.c |
| 12 |  1 |  rcu_read_acquire      |   640  |  include/linux/rcupdate.h |
| 13 |  2 |  lock_acquire          |   238  |  include/linux/rcupdate.h |
| 14 |  1 |  __lock_acquire        |   3554 |   kernel/lockdep.c |
| 15 |  1 |  register_lock_class   |   3041 |   kernel/lockdep.c |
| 16 |  1 |  look_up_lock_class    |   724  |  kernel/lockdep.c |
| 17 | 26 |  dump_stack            |   669  |  kernel/lockdep.c |
| 18 |  3 |  stack_frame           |   188  |  arch/x86/kernel/dumpstack.c |
| 19 |  1 |  get_bp                |   69   | get_bp(bp); |

```
50 #ifdef CONFIG_X86_32
51 #define STACKSLOTS_PER_LINE 8
52 #define get_bp(bp) asm("movl %%ebp, %0" : "=r" (bp) :)
53 #else
54 #define STACKSLOTS_PER_LINE 4
55 #define get_bp(bp) asm("movq %%rbp, %0" : "=r" (bp) :)
56 #endif
```

|    | \# TO tag | FROM | line | in file/text |
|----|:---------:|-------------------|------|-------------|
|1   | 1         |send_sigio_to_task | 606  | fs/fcntl.c |
|2   | 1         |sigio_perm         | 553  | fs/fcntl.c |
|3   | 1         |rcu_read_lock      | 533  | fs/fcntl.c |
|4   | 1         |rcu_read_acquire   |  640 |  include/linux/rcupdate.h |
|5   | 2         |lock_acquire       | 238  | include/linux/rcupdate.h |
|6   | 1         |\_\_lock_acquire     |3554  | kernel/lockdep.c |
|7   | 1         |lockstat_clock     |3098  | kernel/lockdep.c |
|8   | 1         |local_clock        | 158  | kernel/lockdep.c |
|9   | 1         |sched_clock_cpu    | 342  | kernel/sched_clock.c |
|10  |  1        | sched_clock_remote|   256|   kernel/sched_clock.c |
|11  |  1        | sched_clock_local |  192 |  kernel/sched_clock.c |
|12  | 37        | sched_clock       |147   | kernel/sched_clock.c |
|13  |  1        | native_sched_clock|    75|  arch/x86/kernel/tsc.c |
|14  |  1        | rdtscll           | 60   |arch/x86/kernel/tsc.c |
|15  |  1        | \_\_native_read_tsc |  238 | ((val) = \_\_native_read_tsc()) |

```
121 static __always_inline unsigned long long __native_read_tsc(void)
122 {
123         DECLARE_ARGS(val, low, high);
124 
125         asm volatile("rdtsc" : EAX_EDX_RET(val, low, high));
126 
127         return EAX_EDX_VAL(val, low, high);
128 }
```

|  | \# TO tag  |    FROM | line | in file/text |
|-----|:--------|---------|------|--------------|
| 1 | 2  |boot_init_stack_canary |  485 |  init/main.c |
| 2 | 1  |__native_read_tsc      | 74   |arch/x86/include/asm/stackprotector.h |

`__native_read_tsc` is called again.

### boot_cpu_init
\# TO tag         FROM line  in file/text  
1  1 boot_cpu_init     497  init/main.c  
2  1 set_cpu_online    437  init/main.c  
3  1 cpumask_set_cpu   635  kernel/cpu.c  
4 24 set_bit           256  include/linux/cpumask.h  

```
38 static inline void set_bit(int nr, void *addr)
39 {
40         asm("btsl %1,%0" : "+m" (*(u32 *)addr) : "Ir" (nr));
41 }
```

\# TO tag         FROM line  in file/text  
1  1 boot_cpu_init     497  init/main.c  
2  1 set_cpu_online    437  init/main.c  
3  1 cpumask_clear_cpu   637  kernel/cpu.c  
4 24 clear_bit         266  include/linux/cpumask.h  

```
97 static __always_inline void
98 clear_bit(int nr, volatile unsigned long *addr)
99 {
100         if (IS_IMMEDIATE(nr)) {
101                 asm volatile(LOCK_PREFIX "andb %1,%0"
102                         : CONST_MASK_ADDR(nr, addr)
103                         : "iq" ((u8)~CONST_MASK(nr)));
104         } else {
105                 asm volatile(LOCK_PREFIX "btr %1,%0"
106                         : BITOP_ADDR(addr)
107                         : "Ir" (nr));
108         }
109 }
```
### setup_arch
\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 visws_early_detect   774  arch/x86/kernel/setup.c  
3 20 inb_p             222  arch/x86/platform/visws/visws_quirks.c  
4 28 inb               136  include/asm-generic/io.h  

```
 48 static inline u8 inb(u16 port)
 49 {
 50         u8 v;
 51         asm volatile("inb %1,%0" : "=a" (v) : "dN" (port));
 52         return v;
 53 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 visws_early_detect   774  arch/x86/kernel/setup.c  
3 20 outb_p            264  arch/x86/platform/visws/visws_quirks.c  
4 30 outb              139  include/asm-generic/io.h  

```
44 static inline void outb(u8 v, u16 port)
45 {
46         asm volatile("outb %0,%1" : : "a" (v), "dN" (port));
47 }
```

`visws_early_detect` function calls `inb_p` and `outb_p` multiple times in function body.

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 load_cr3          784  arch/x86/kernel/setup.c  
3  3 write_cr3         187  arch/x86/include/asm/processor.h  
4  1 native_write_cr3   330  native_write_cr3(x);  


```
247 static inline void native_write_cr3(unsigned long val)
248 {
249         asm volatile("mov %0,%%cr3": : "r" (val), "m" (__force_order));
250 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  4 \_\_flush_tlb_all   785  arch/x86/kernel/setup.c  
3  6 \_\_flush_tlb_global    52  arch/x86/include/asm/tlbflush.h  
4  1 \_\_native_flush_tlb_global    14  arch/x86/include/asm/tlbflush.h  
5  1 native_write_cr4    37  arch/x86/include/asm/tlbflush.h  


```
275 static inline void native_write_cr4(unsigned long val)
276 {       
277         asm volatile("mov %0,%%cr4": : "r" (val), "m" (__force_order));
278 }
```

Function `early_trap_init`, `early_cpu_init` and `early_ioremap_init` have not found tags.


\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 mtrr_bp_init      951  arch/x86/kernel/setup.c  
3  1 get_mtrr_state    667  arch/x86/kernel/cpu/mtrr/main.c  
4  1 get_fixed_ranges   405  arch/x86/kernel/cpu/mtrr/generic.c  
5  1 k8_check_syscfg_dram_mod_en   280  arch/x86/kernel/cpu/mtrr/generic.c  
6  1 rdmsr              57  arch/x86/kernel/cpu/mtrr/generic.c  
7  1 native_read_msr   150  u64 __val = native_read_msr((msr));
                

```
68 static inline unsigned long long native_read_msr(unsigned int msr)
69 {
70         DECLARE_ARGS(val, low, high);
71 
72         asm volatile("rdmsr" : EAX_EDX_RET(val, low, high) : "c" (msr));
73         return EAX_EDX_VAL(val, low, high);
74 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 mtrr_bp_init      951  arch/x86/kernel/setup.c  
3  1 get_mtrr_state    667  arch/x86/kernel/cpu/mtrr/main.c  
4  1 get_fixed_ranges   405  arch/x86/kernel/cpu/mtrr/generic.c  
5  1 k8_check_syscfg_dram_mod_en   280  arch/x86/kernel/cpu/mtrr/generic.c  
6  1 mtrr_wrmsr         63  arch/x86/kernel/cpu/mtrr/generic.c  
7  1 wrmsr_safe        461  arch/x86/kernel/cpu/mtrr/generic.c  
8  1 native_write_msr_safe   169  return native_write_msr_safe(msr, low, high);  

```
 99 notrace static inline int native_write_msr_safe(unsigned int msr,
 100                                         unsigned low, unsigned high)
 101 {    
 102         int err;
 103         asm volatile("2: wrmsr ; xor %[err],%[err]\n"
 104                      "1:\n\t"     
 105                      ".section .fixup,\"ax\"\n\t" 
 106                      "3:  mov %[fault],%[err] ; jmp 1b\n\t"
 107                      ".previous\n\t"   
 108                      _ASM_EXTABLE(2b, 3b)
 109                      : [err] "=a" (err)    
 110                      : "c" (msr), "0" (low), "d" (high),
 111                        [fault] "i" (-EIO)
 112                      : "memory");
 113         return err;       
 114 }                           
```


\# TO tag         FROM line  in file/text  
 1 29 setup_arch        500  init/main.c  
 2  2 mtrr_bp_init      951  arch/x86/kernel/setup.c  
 3  1 get_mtrr_state    667  arch/x86/kernel/cpu/mtrr/main.c  
 4  2 prepare_set       428  arch/x86/kernel/cpu/mtrr/generic.c  
 5  6 wbinvd            681  arch/x86/kernel/cpu/mtrr/generic.c  
 6  1 emulator_wbinvd  4704  .wbinvd              = emulator_wbinvd,  
 7  1 kvm_emulate_wbinvd  4446  kvm_emulate_wbinvd(emul_to_vcpu(ctxt));  
                

 ```
 4425 int kvm_emulate_wbinvd(struct kvm_vcpu *vcpu)
 ```

 THis function is in `arch/x86/kvm/x86.c`. kvm will do emulation on `wbinvd` when booting system. 

\# TO tag         FROM line  in file/text  
 1 29 setup_arch        500  init/main.c  
 2  2 mtrr_bp_init      951  arch/x86/kernel/setup.c  
 3  1 get_mtrr_state    667  arch/x86/kernel/cpu/mtrr/main.c  
 4  2 prepare_set       428  arch/x86/kernel/cpu/mtrr/generic.c  
 5  5 \_\_flush_tlb       690  arch/x86/kernel/cpu/mtrr/generic.c  
 6  1 \_\_native_flush_tlb    13  arch/x86/include/asm/tlbflush.h  
 7  1 native_write_cr3    20  arch/x86/include/asm/tlbflush.h  


```
247 static inline void native_write_cr3(unsigned long val)
248 {       
249         asm volatile("mov %0,%%cr3": : "r" (val), "m" (__force_order));
250 }       
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 mtrr_bp_init      951  arch/x86/kernel/setup.c  
3  1 get_mtrr_state    667  arch/x86/kernel/cpu/mtrr/main.c  
4  2 post_set          432  post_set();  


```
700 static void post_set(void) __releases(set_atomicity_lock)
701 {       
702         /* Flush TLBs (no need to flush caches - they are disabled) */
703         __flush_tlb();
704 
705         /* Intel (P6) standard MTRRs */
706         mtrr_wrmsr(MSR_MTRRdefType, deftype_lo, deftype_hi);
707         
708         /* Enable caches */
709         write_cr0(read_cr0() & 0xbfffffff);
710                 
711         /* Restore value of CR4 */
712         if (cpu_has_pge)
713                 write_cr4(cr4);
714         raw_spin_unlock(&set_atomicity_lock);
715 }       
```
\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 init_memory_mapping  1016  arch/x86/kernel/setup.c  
3  1 set_in_cr4        161  arch/x86/mm/init.c  
4  3 write_cr4         608  arch/x86/include/asm/processor.h  
5  1 native_write_cr4   345  native_write_cr4(x);  


```
275 static inline void native_write_cr4(unsigned long val)
276 {
277         asm volatile("mov %0,%%cr4": : "r" (val), "m" (__force_order));
278 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 init_memory_mapping  1016  arch/x86/kernel/setup.c  
3  4 kernel_physical_mapping_init   280  arch/x86/mm/init.c  
4  1 phys_pud_init     590  arch/x86/mm/init_64.c  
5 13 pud_populate      549  arch/x86/mm/init_64.c  
6 42 flush_tlb_mm      171  arch/x86/mm/pgtable.c  
7  2 leave_mm          295  arch/x86/mm/tlb.c  
8  1 load_cr3           68  arch/x86/mm/tlb.c  
9  3 write_cr3         187  arch/x86/include/asm/processor.h  
10  1 native_write_cr3   330  native_write_cr3(x);  


```
247 static inline void native_write_cr3(unsigned long val)
248 {
249         asm volatile("mov %0,%%cr3": : "r" (val), "m" (__force_order));
250 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 init_ohci1394_dma_on_all_controllers  1048  arch/x86/kernel/setup.c  
3  2 read_pci_config   282  drivers/firewire/init_ohci1394_dma.c  
4 27 outl               13  arch/x86/pci/early.c  


```
66 static inline void outl(u32 v, u16 port)
67 {
68         asm volatile("outl %0,%1" : : "a" (v), "dN" (port));
69 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  2 vsmp_init        1057  arch/x86/kernel/setup.c  
3  1 detect_vsmp_box   154  arch/x86/kernel/vsmp_64.c  
4  2 read_pci_config   128  arch/x86/kernel/vsmp_64.c  
5 27 outl               13  arch/x86/pci/early.c  

```
10 u32 read_pci_config(u8 bus, u8 slot, u8 func, u8 offset)
11 {
12         u32 v;
13         outl(0x80000000 | (bus<<16) | (slot<<11) | (func<<8) | offset, 0xcf8);
14         v = inl(0xcfc);
15         return v;
16 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 kvmclock_init    1072  arch/x86/kernel/setup.c  
3  1 kvm_para_has_feature   181  arch/x86/kernel/kvmclock.c  
4  4 kvm_arch_para_features    32  include/linux/kvm_para.h  
5  1 cpuid_eax         187  arch/x86/include/asm/kvm_para.h  
6 21 cpuid             670  cpuid(op, &eax, &ebx, &ecx, &edx);  
7  2 \_\_cpuid           650  \_\_cpuid(eax, ebx, ecx, edx);  
8  1 native_cpuid      573  #define \_\_cpuid native_cpuid  


```
172 static inline void native_cpuid(unsigned int *eax, unsigned int *ebx,
173                                 unsigned int *ecx, unsigned int *edx)
174 {
175         /* ecx is often an input as well as an output. */
176         asm volatile("cpuid"
177             : "=a" (*eax),
178               "=b" (*ebx),
179               "=c" (*ecx),
180               "=d" (*edx)
181             : "0" (*eax), "2" (*ecx)
182             : "memory");
183 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 kvmclock_init    1072  arch/x86/kernel/setup.c  
3  1 kvm_register_clock   190  arch/x86/kernel/kvmclock.c  
4  1 native_write_msr_safe   132  arch/x86/kernel/kvmclock.c  


```
98 /* Can be uninlined because referenced by paravirt */
99 notrace static inline int native_write_msr_safe(unsigned int msr,
100                                         unsigned low, unsigned high)
101 {       
102         int err; 
103         asm volatile("2: wrmsr ; xor %[err],%[err]\n"
104                      "1:\n\t"
105                      ".section .fixup,\"ax\"\n\t"
106                      "3:  mov %[fault],%[err] ; jmp 1b\n\t"
107                      ".previous\n\t"
108                      _ASM_EXTABLE(2b, 3b)
109                      : [err] "=a" (err)
110                      : "c" (msr), "0" (low), "d" (high),
111                        [fault] "i" (-EIO)
112                      : "memory");
113         return err;
114 }
```

\# TO tag         FROM line  in file/text  
1 29 setup_arch        500  init/main.c  
2  1 tboot_probe      1091  arch/x86/kernel/setup.c  
3  6 set_fixmap         83  arch/x86/kernel/tboot.c  
4  5 \_\_set_fixmap      179  arch/x86/include/asm/fixmap.h  
5  1 native_set_fixmap   174  arch/x86/include/asm/fixmap.h  
6  1 \_\_native_set_fixmap   446  arch/x86/mm/pgtable.c  
7  1 set_pte_vaddr     439  arch/x86/mm/pgtable.c  
8  1 set_pte_vaddr_pud   229  arch/x86/mm/init_64.c  
9  5 \_\_flush_tlb_one   212  arch/x86/mm/init_64.c  
10  2 \_\_flush_tlb_single    60  \_\_flush_tlb_single(addr);  
11  1 \_\_native_flush_tlb_single    15  #define \_\_flush_tlb_single(addr) __native_flush_tlb_single(addr)  


```
 44 static inline void __native_flush_tlb_single(unsigned long addr)
 45 {       
 46         asm volatile("invlpg (%0)" ::"r" (addr) : "memory");
 47 }       
 48                 
```

### setup_per_cpu_areas

\# TO tag         FROM line  in file/text  
1  6 setup_per_cpu_areas   505  init/main.c  
2  1 switch_to_new_gdt   267  arch/x86/kernel/setup_percpu.c  
    3  1 load_gdt          377  arch/x86/kernel/cpu/common.c  
4  1 native_load_gdt    89  #define load_gdt(dtr)^I^I^I^Inative_load_gdt(dtr)  


```
213 static inline void native_load_gdt(const struct desc_ptr *dtr)
214 {
215         asm volatile("lgdt %0"::"m" (*dtr));
216 }
```

\# TO tag         FROM line  in file/text  
1  6 setup_per_cpu_areas   505  init/main.c  
2  1 switch_to_new_gdt   267  arch/x86/kernel/setup_percpu.c  
3  1 load_percpu_segment   380  arch/x86/kernel/cpu/common.c  
4  1 wrmsrl            362  arch/x86/kernel/cpu/common.c  
5  1 native_write_msr   164  native_write_msr((msr), (u32)((u64)(val)), (u32)((u64)(val) >> 32))  

```
92 static inline void native_write_msr(unsigned int msr,
93                                     unsigned low, unsigned high)
94 {
95         asm volatile("wrmsr" : : "c" (msr), "a"(low), "d" (high) : "memory");
96 }
97 
```

###trap_init

\# TO tag         FROM line  in file/text  
1 30 trap_init         527  init/main.c  
2  9 cpu_init          741  arch/x86/kernel/traps.c  
3  1 load_idt         1183  arch/x86/kernel/cpu/common.c  
4  1 native_load\_idt    90  #define load\_idt(dtr)  native_load_idt(dtr)  
5  1 lidt              220  asm volatile("lidt %0"::"m" (*dtr));  

```
218 static inline void native_load_idt(const struct desc_ptr *dtr)
219 {
220         asm volatile("lidt %0"::"m" (*dtr));
221 }
```

\# TO tag         FROM line  in file/text  
1 30 trap_init         527  init/main.c  
2  9 cpu_init          741  arch/x86/kernel/traps.c  
3  1 load_TR_desc     1225  arch/x86/kernel/cpu/common.c  
4  1 native_load_tr_desc    88  #define load_TR_desc()^I^I^I^Inative_load_tr_desc()  

```
208 static inline void native_load_tr_desc(void)
209 {
210         asm volatile("ltr %w0"::"q" (GDT_ENTRY_TSS*8));
211 }
```

\# TO tag         FROM line  in file/text  
1 30 trap_init         527  init/main.c  
2  9 cpu_init          741  arch/x86/kernel/traps.c  
3  1 load_LDT         1226  arch/x86/kernel/cpu/common.c  
4  1 load_LDT_nolock   283  load_LDT_nolock(pc);  
5  1 set_ldt           277  set_ldt(pc->ldt, pc->size);  
6  1 native_set_ldt     99  #define set_ldt native_set_ldt  

```
192 static inline void native_set_ldt(const void *addr, unsigned int entries)
193 {
194         if (likely(entries == 0))
195                 asm volatile("lldt %w0"::"q" (0));
196         else {
197                 unsigned cpu = smp_processor_id();
198                 ldt_desc ldt;
199 
200                 set_tssldt_descriptor(&ldt, (unsigned long)addr, DESC_LDT,
201                                       entries * LDT_ENTRY_SIZE - 1);
202                 write_gdt_entry(get_cpu_gdt_table(cpu), GDT_ENTRY_LDT,
203                                 &ldt, DESC_LDT);
204                 asm volatile("lldt %w0"::"q" (GDT_ENTRY_LDT*8));
205         }
206 }
207 
```

\# TO tag         FROM line  in file/text  
1 30 trap_init         527  init/main.c  
2  9 cpu_init          741  arch/x86/kernel/traps.c  
3  3 fpu_init         1231  arch/x86/kernel/cpu/common.c  
4  3 write_cr0         108  arch/x86/kernel/i387.c  
5  1 native_write_cr0   310  native_write_cr0(x);  

```
223 static inline void native_write_cr0(unsigned long val)
224 {
225         asm volatile("mov %0,%%cr0": : "r" (val), "m" (__force_order));
226 }
```
