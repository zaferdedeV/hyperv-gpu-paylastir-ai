# Hyper-V ile Sanal Makinelerde GPU Paylaşımı ve Yapay Zeka Projelerinde Kullanımı

Merhaba! Bu rehberde, Hyper-V kullanarak sanal makinelerde GPU paylaşımını nasıl gerçekleştireceğinizi ve bu yapılandırmanın yapay zeka projeleri için nasıl kullanılabileceğini adım adım anlatacağım.

---

## 🎯 **Proje Hedefleri**

- Hyper-V kurulumu ve yapılandırması
- Sanal makinelerde GPU paylaşımı (GPU-P)
- Yapay zeka sanallaştırma projeleri için altyapı oluşturma

---

## 🛠️ **1. Hyper-V Kurulumu**

1. **Windows Özelliklerini Aç:**  
   - **Win + R** → `optionalfeatures` yazın → Enter.  
2. **Açılan pencerede şu kutucukları işaretleyin:**  
   - **Hyper-V**  
   - **Windows Hypervisor Platform**  
   - **Virtual Machine Platform**  
3. **Tamam** deyin ve sistemi yeniden başlatın.  

👉 **Kontrol:** Yeniden başladıktan sonra **Başlat Menüsü → Hyper-V Manager** yazıp çalıştırın.

---

## 🖥️ **2. Sanal Anahtar (Network) Ayarı**

1. **Hyper-V Manager’i Açın**.  
2. **Sağ tarafta → Virtual Switch Manager**.  
3. **New Virtual Network Switch → External → Create**.  
4. **İsim verin (Örn: ExternalSwitch)** ve **fiziksel ağ kartınızı seçin**.  

---

## 💽 **3. Sanal Makineleri Oluşturma**

### 🧱 **Her Sanal Makine için:**

1. **Hyper-V Manager’de → New → Virtual Machine**.  
2. **Ad:** `AI-VM-1` / `AI-VM-2`  
3. **Generation:** **Gen 2** (UEFI desteği için).  
4. **RAM:** **8 GB (8192 MB)** (Dinamik RAM'i kapatın).  
5. **CPU:** 4 Çekirdekli CPU varsa → **2 vCPU** ayarlayın.  
6. **Network:** Az önce oluşturduğunuz **ExternalSwitch**’i seçin.  
7. **Disk:** **VHDX (dynamic)** ve en az **80 GB** alan verin.  

---

## ⚙️ **4. CPU Bölme İşlemi (CPU Affinity)**

### **Her VM İçin CPU Çekirdeklerini Ayarlama:**

1. **PowerShell’i Yönetici Olarak Açın:**
2. **Makineye 2 CPU çekirdeği vermek için:**
   ```powershell
   Set-VMProcessor -VMName "AI-VM-1" -Count 2
   Set-VMProcessor -VMName "AI-VM-2" -Count 2
   ```
3. **Performans için sanallaştırma uzantılarını açın:**
   ```powershell
   Set-VMProcessor -VMName "AI-VM-1" -ExposeVirtualizationExtensions $true
   Set-VMProcessor -VMName "AI-VM-2" -ExposeVirtualizationExtensions $true
   ```

---

## 🎨 **5. GPU Bölümlendirme (GPU-P) Ayarı**

**RTX 4090 kartım için yaptığım işlemler:**

1. **PowerShell’de GPU'yu görüntüleyin:**
   ```powershell
   Get-VMPartitionableGpu
   ```
2. **GPU bölümlendirmeyi açın:**
   ```powershell
   Set-VMPartitionableGpu -PartitionableGpuName "NVIDIA GeForce RTX 4090" -MaxPartitionCount 2
   ```
3. **Her sanal makineye GPU-P ekleyin:**
   ```powershell
   Add-VMGpuPartitionAdapter -VMName "AI-VM-1"
   Add-VMGpuPartitionAdapter -VMName "AI-VM-2"
   ```
4. **Sanal makineleri başlatın ve GPU sürücülerini yükleyin.**

---

## 🛠️ **6. Kontrol ve Test**

1. **Her iki sanal makineyi başlatın.**  
2. **Görev Yöneticisi → Performans** sekmesinde CPU ve GPU kullanımlarını kontrol edin.  
3. **Sanal makinelerde `dxdiag` komutunu çalıştırın** → **Ekran Kartı (Display)** sekmesinde **4090** görünmeli.  

---

## 🚀 **Yapay Zeka Projelerinde Kullanımı**

Bu yapılandırma, yapay zeka projeleri için ideal bir altyapı sunar. GPU paylaşımı sayesinde birden fazla sanal makinede derin öğrenme, model eğitimi ve test işlemlerini verimli şekilde gerçekleştirebilirsiniz.

