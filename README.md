# Python basic EDA
## Overview
The project use Pandas, Matplotlib, and Seaborn library to build an ETL pipeline, clean data, and perform Exploratory Data Analysis (EDA) tasks from 2 mock datasets of 6-month transaction status of an imaginary company with ~1000 records.
This project involves:
1. **Building ETL Pipeline**: Create a Python function to get data from .csv files, clean data, perform EDA, and load.  
2. **Building Dashboards**: Using Matplotlib and Seaborn library to perform data visualization. <br>  

:dart: **Target:** Build dashboards serving data analytical tasks.
<br> <br>
**Tool use:** Google Colab  
Colab link: https://colab.research.google.com/drive/1qsGXKG2r0xVvjwwBlggaUjjneOza0b3i#scrollTo=bUDeAeVwRBed&uniqifier=6

---
## Building ETL Pipeline
### Datasets
_`giao_dich.csv`_  
Sample of first 5 rows:
| MaGD | MaKH | NgayGD | GioGD | LoaiGD | SoTien | ChiNhanh | KenhGD | TrangThai | GhiChu |
|---|---|---|---|-----------|---|---|---|---|---|
| GD000508 | KH0124 | 2024-02-17 | 16:16 | Mua ngoại tệ | 165717229 | Hà Nội | Mobile App | Thành công | nan |
| GD000082 | KH0198 | 2024-01-08 | 16:04 | Nạp tiền | 20738329 | Khánh Hòa | ATM | Thành công | nan |
| GD000091 | KH0033 | 2024-01-24 | 13:07 | Chuyển khoản | 154100866 | Đồng Nai | Internet Banking | Thành công | nan |
| GD000193 | KH0095 | 2024-06-30 | 16:02 | Chuyển khoản | 58005231 | Nghệ An | POS | Thành công | nan |
| GD000474 | KH0133 | 2024-06-02 | 17:27 | Rút tiền | 165478158 | Đồng Nai | POS | Thành công | nan |

_`khach_hang_crm.csv`_  
Sample of first 5 rows:
| MaKH | HoTen | GioiTinh | Tuoi | NgheNghiep | NhomKH | Email | SoDienThoai |
|---|---|---|---|---|---|---|---|
| KH0001 | Phạm Văn Ích | Nam | 34 | Giáo viên | Cá nhân thường | kh0001@email.com | 983197857 |
| KH0002 | Lê Bảo Oanh | Nam | 21 | Kinh doanh tự do | Cá nhân VIP | kh0002@email.com | 941227216 |
| KH0003 | Tạ Bảo An | Nam | 65 | Luật sư | Doanh nghiệp lớn | kh0003@email.com | 939587039 |
| KH0004 | Dương Bảo Ích | Nam | 68 | Giáo viên | Doanh nghiệp lớn | kh0004@email.com | 955667651 |
| KH0005 | Võ Hoàng Giang | Nữ | 26 | Kinh doanh tự do | Doanh nghiệp lớn | kh0005@email.com | 922981052 |

### Schema
<!--[](visualization/python_eda_schema.drawio.png)-->
<img src="visualization/python_eda_schema.drawio.png" height="600">

### ETL Pipeline
```mermaid
graph TD
    %% Define global styles for shapes
    classDef process fill:#ffffff,stroke:#000000,stroke-width:1px,color:#000000
    classDef note fill:#e2e8f0,stroke:#94a3b8,stroke-width:1px,color:#333333,stroke-dasharray: 3 3
    classDef diamond fill:#ffffff,stroke:#000000,stroke-width:1px,color:#000000

    subgraph Extract [1. EXTRACT]
        A[giao_dich.csv <br/> Transaction Data]:::process
        B[khach_hang_crm.csv <br/> Customer Data]:::process
    end

    subgraph Transform [2. TRANSFORM]
        C[Clean Transactions]:::process
        C_note[Drop duplicates, fill missing<br/>branches & channels]:::note -.- C
        
        D[Format Data]:::process
        D_note[Standardize text casing, extract<br/>Month/Hour from Date/Time]:::note -.- D
        
        E[Flag Errors]:::process
        E_note[Identify SoTien < 0 <br/> as 'GD âm']:::note -.- E
        
        G{Merge Datasets}:::diamond
        G_note[Left Join on MaKH]:::note -.- G
        
        H[Categorize Age]:::process
        H_note[Apply Gen Z, Millennials,<br/>Gen X, Boomer logic]:::note -.- H
        
        I[Filter Data]:::process
        I_note[Remove 'GD âm'<br/>Create df_filtered]:::note -.- I

        %% Transform internal flow routing
        C --> D --> E --> G
        G --> H --> I
    end

    %% Connections linking Extract to Transform
    A --> C
    B --> G

    subgraph Load [3. LOAD / REPORT GENERATION]
        J[rp_gd_thang <br/> Monthly volume & value]:::process
        K[rp_nhomkh <br/> Metrics by customer group]:::process
        
        L[rp_kenhgd_kh <br/> Channels by customer group]:::process
        M[rp_mobile_app <br/> Filtered for Mobile App only]:::process
        
        N[rp_kenhgd <br/> Value by channel]:::process
        O[rp_summary <br/> High-level KPIs & Digital %]:::process
        
        P[rp_nhomtuoi <br/> Metrics by age demographic]:::process
        Q[rp_top20 <br/> Top 20 VIPs by total value]:::process
        
        %% Vertical parent/child dependencies inside the Load layer
        L --> M
        N --> O
    end

    %% Fan out from Filter Data to all independent reports
    I --> J
    I --> K
    I --> L
    I --> N
    I --> P
    I --> Q
    
    %% Style the background containers
    style Extract fill:#f8fafc,stroke:#64748b,stroke-width:2px,color:#000000
    style Transform fill:#f8fafc,stroke:#64748b,stroke-width:2px,color:#000000
    style Load fill:#f8fafc,stroke:#64748b,stroke-width:2px,color:#000000
```
---
## Building Dashboards
![](visualization/overall_status.png) 
--
![](visualization/customer_group.png) 
--
![](visualization/age_group.png) 
--
![](visualization/channels.png) 
--
![](visualization/top20.png) 

