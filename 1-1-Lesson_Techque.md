<img src="picture\images-0.png" alt="alt text" width="100">

<br>


# วิธีการใช้ Parameter [Transform]
> ✍️ มีประโยชน์เวลาเราทำ PowerBI แล้วหากลอง Test ใช้ DB_DEV  
> → พอขึ้นระบบ ใช้ DB_PRD ไม่ต้องมาเปลี่ยนอะไรใหม่ แค่ปรับ Parameter อย่างเดียว
<details>
<summary> ก่อนสร้าง Parameter </summary>
<div style="margin-left:1em">

`= Sql.Databases("10.1.1.124")`

<img src="picture\Techque\image-1.png" width="500">  

`= TNTL_PUR_DEV{[Schema="dbo",Item="VW_FACT_MS_FORM"]}[Data]`

<img src="picture\Techque\image-2.png" width="500"> 
</div>
</details>

<details>
<summary> หลังสร้าง Parameter </summary>
<div style="margin-left:1em">

<img src="picture\Techque\image-3.png" width="300">   

`= Sql.Database("10.1.1.124", "" &DB& "")` **<< Database ลบ s**

<img src="picture\Techque\image-4.png" width="500"> 

`= Source{[Schema="dbo",Item="VW_FACT_MS_FORM"]}[Data]` **<< Source**

<img src="picture\Techque\image-5.png" width="500">
</div>
</details>

<br>

# การทำ YTD

<img src="picture\Techque\image-6.png" width="300"> <br>

<img src="picture\Techque\image-7.png" width="300"> <br>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> 1. สร้าง Table >> Date </summary>
<div style="margin-left:1em">

1. ไปที่ Modeling >> New table >> สร้าง Date <br>
    ``` md
    Date = 
    CALENDAR(
        MIN(M_BI_KPed_U[DocDate]),
        MAX(M_BI_KPed_U[DocDate])
    )
    ```

2. สร้าง Measure >> สร้าง Date Slicer Filter <br>
    ``` md
    Date Slicer Filter = 
    IF(
        SELECTEDVALUE('Date_Periods'[Type]) = "Custom",
        1,
        0
    )
    ```

3. สร้าง Measure >> สร้าง Date Slicer Title <br>
    ``` md
    Date Slicer Title = 
    IF(
        SELECTEDVALUE('Date_Periods'[Type]) = "Custom",
        "Choose a custom date range",
        "Custom selection disabled")
    ```
</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> 2. สร้าง Table >> Date_Periods </summary>
<div style="margin-left:1em">

- ไปที่ Modeling >> New table

    <details>
    <summary> Code 1 </summary>
    <div style="margin-left:1em">

    ``` md
    Date_Periods = 
    UNION(
        ADDCOLUMNS(
            DATESMTD('Date'[Date]),
            "Type", "MTD",
            "Sort", 1
        ),
        ADDCOLUMNS(
            DATESQTD('Date'[Date]),
            "Type", "QTD",
            "Sort", 2
        ),
        ADDCOLUMNS(
            DATESYTD('Date'[Date]),
            "Type", "YTD",
            "Sort", 3
        ),
        ADDCOLUMNS(
            PREVIOUSMONTH(DATESMTD('Date'[Date])),
            "Type", "Last Month",
            "Sort", 4
        ),
        ADDCOLUMNS(
            PREVIOUSQUARTER(DATESQTD('Date'[Date])),
            "Type", "Last Qtr",
            "Sort", 5
        ),
        ADDCOLUMNS(
            PREVIOUSYEAR(DATESYTD('Date'[Date])),
            "Type", "Last Year",
            "Sort", 6
        ),
        ADDCOLUMNS(
            CALENDAR(MIN('Date'[Date]),MAX('Date'[Date])),
            "Type", "All Time",
            "Sort", 7
        ),
        ADDCOLUMNS(
            CALENDAR(MIN('Date'[Date]),MAX('Date'[Date])),
            "Type", "Custom",
            "Sort", 8
        )
    )
    ```
    </div>
    </details>

    <details>
    <summary> Code 2 </summary>
    <div style="margin-left:1em">

    ``` md
    Date Periods = 
    UNION(
        ADDCOLUMNS(
            DATESYTD('Date'[Date]),
            "Type", "YTD",
            "Sort", 2
        ),
        ADDCOLUMNS(
            CALENDAR("1/1/2022",TODAY()-366),
            "Type", "YTD",
            "Sort", 2
        ),
        ADDCOLUMNS(
            CALENDAR(MIN('Date'[Date]),MAX('Date'[Date])),
            "Type", "Full Year",
            "Sort", 1
        ),
        ADDCOLUMNS(
            CALENDAR(MIN('Date'[Date]),MAX('Date'[Date])),
            "Type", "Custom",
            "Sort", 8
        )
    )
    ```
    </div>
    </details>


</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> 3. เชื่อม Relation ตามนี้ </summary>
<div style="margin-left:1em">

1. ‘M_BI_KPed_U’[DocDate] >> เชื่อมกับ Date[Date]   (Many to One แบบ1ทาง)  
2. ‘Date_Periods[Date]         >> เชื่อมกับ Date[Date]  (Many to One แบบ2ทาง)  

> หมายเหตุ : M_BI_KPed_U เป็นตารางหลักข้อมูลเรา

- Relation <br>
    <img src="picture\Techque\image-8.png" width="500"> <br>

- **การใช้งาน**  <br>
    ใช้ Filter >> Type

    <img src="picture\Techque\image-9.png" width="500"> <br>

    <details>
    <summary> Example of working at ACT </summary>
    <div style="margin-left:1em">

    <img src="picture\Techque\image-10.png" width="500"> <br>

    <img src="picture\Techque\image-11.png" width="500"> <br>
    </div>

    </details>

</div>
</details>

<br>

# DataType

| Data Type              | Description |
|------------------------|-------------|
| Decimal Number         | แปลงข้อมูลเป็นเลขฐานสิบ |
| Fixed Decimal Number   | แปลงข้อมูลเป็นเลขฐานสิบด้วยจำนวนทศนิยมคงที่ |
| Whole Number           | แปลงข้อมูลเป็นเลขจำนวนเต็ม |
| Percentage             | แปลงข้อมูลเป็นเปอร์เซ็นต์ |
| Date/Time              | แปลงเป็นชนิดข้อมูลวันที่และเวลา |
| Date                   | แปลงเป็นชนิดข้อมูลวันที่ |
| Time                   | แปลงเป็นชนิดข้อมูลเวลา |
| Date/Time/Timezone     | แปลงเป็นชนิดข้อมูลวันที่ เวลา และโซนเวลา |
| Duration               | ตั้งค่าข้อมูลเป็นระยะเวลา ใช้สำหรับการคำนวณวันที่และเวลา |
| Text                   | ตั้งค่าเป็นชนิดข้อมูลข้อความ |
| True/False             | ตั้งค่าชนิดข้อมูลเป็นบูลีน (จริง/เท็จ) |
| Binary                 | กำหนดข้อมูลเป็นไบนารี ซึ่งไม่สามารถอ่านได้โดยตรง |


<br>

# Power BI Service

## 1. Connect with SharePoint & Set Connect บน Power BI Service

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> วิธีการ Connect SharePoint บน Power BI Desktop </summary>
<div style="margin-left:1em">

1. Go to Sharepoint after that **Copy Link** <br>
    <img src="picture\Techque\image-12.png" width="400"> <br>

2. Choose Only people with existing access → press Apply <br>
    <img src="picture\Techque\image-13.png" width="300"> <br>

3. You will get the following link: >> Cut out unwanted text <br>
    <img src="picture\Techque\image-14.png" width="500"> <br>

4. Get Data >> Choose **Web** <br>
    <img src="picture\Techque\image-15.png" width="500"> <br>

5. input Website from (3.) >> go to **Organizational account** >> sign in with my mail (With access to sharepoint) >> Connect >> Choose source >> Load <br>
    <img src="picture\Techque\image-16.png" width="500"> <br>
    <img src="picture\Techque\image-17.png" width="500"> <br>

6. Create Visualize >> Publish
</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> วิธี Set Connect SharePoint Power BI Service </summary>
<div style="margin-left:1em">

1. After publish on Power BI service >> go to data set that we set connect >> Settings <br>
    <img src="picture\Techque\image-18.png" width="500"> <br>


2. Data source credentails >> Edit credentials >> Choose OAuth2 >> Choose Organizarional >> sign in (With access to sharepoint)
</div>
</details>

## 2. App vs Report

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> What is a Apps? </summary>
<div style="margin-left:1em">

- **Apps** are ***content packages in Power BI Service*** that make it easier for end users to access data.
- An App can contain multiple components, such as:
    - Reports
    - Dashboards
    - Workbooks
    - Datasets (used in the background for refresh)
- Apps are typically used to **publish** content to groups of users or teams in a well-organized way with clearly defined permissions.
- **Think of it as:** A finished website where users can only see the pages you allow them to see, without browsing through all the files.

<details>
<summary> TH version </summary>
<div style="margin-left:1em;">
</div>

- **Apps** เป็น***แพ็กเกจรวมเนื้อหา (content) ใน Power BI Service*** เพื่อให้ผู้ใช้ปลายทาง เข้าถึงข้อมูลได้ง่าย
- ภายใน App สามารถมีได้หลายอย่าง เช่น
    - Reports
    - Dashboards
    - Workbooks
    - Datasets (เบื้องหลังสำหรับ Refresh)
- มักจะใช้สำหรับ **เผยแพร่ (publish)** เนื้อหาไปให้กลุ่มผู้ใช้หรือทีมงานแบบเป็นระเบียบและมีสิทธิ์กำหนดชัดเจน
- **เปรียบเหมือน:** เว็บไซต์สำเร็จรูปที่ผู้ใช้เข้ามาเจอเฉพาะหน้าเพจที่เราจัดไว้ให้ ไม่ต้องไปค้นหาหรือเปิดไฟล์เองใน Workspace
</details>

</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> What is a Report in Workspace? </summary>
<div style="margin-left:1em">

- **Reports** are files created from Power BI Desktop or directly in Power BI Service.
- They live inside a **Workspace** and are used for building, updating dashboards, or viewing raw data.
- A Report is just **one component** that can be published as part of an App.
- **Think of it as:** A Word or PowerPoint file your team is still editing in a shared folder.

<details>
<summary> TH version </summary>
<div style="margin-left:1em;">
</div>

- **Report** คือไฟล์รายงานที่สร้างจาก Power BI Desktop หรือ Power BI Service
- อยู่ใน Workspace และใช้เพื่อสร้าง/ปรับปรุง Dashboard หรือให้ดูข้อมูลโดยตรง
- Report เป็น **ส่วนประกอบหนึ่ง** ที่สามารถถูกนำไปเผยแพร่ผ่าน App ได้
- **เปรียบเหมือน:** ไฟล์ Word หรือ PowerPoint ที่เรายังแก้ไขอยู่ในโฟลเดอร์ของทีม
</details>

</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> Differences between App and Report. </summary>
<div style="margin-left:1em">

| Aspect            | App                                   | Report in Workspace                              |
|-------------------|----------------------------------------|--------------------------------------------------|
| Target Audience   | End users (view-only)                  | Teams who build and edit reports                 |
| Permissions       | Users only see the content the publisher chooses | Users with workspace access can see and edit |
| Purpose           | Package and publish content in a clean, organized way | Develop, edit, and view the raw report |
| Content           | Can include multiple Reports, Dashboards, and Datasets | Only one single report (or file) |
| Updates           | Users see updates only after the App is republished | Changes are visible immediately in the Workspace |

</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> Summary </summary>
<div style="margin-left:1em">

- **Workspace:** Best for development teams and analysts – used to create and edit reports.
- **App:** Best for delivering finished content to end users – stable, organized, and with restricted access.
</div>
</details>

<details style="background-color:#FFF9E5; cursor:pointer; padding: 0.55em 1em; border-radius: 6px; margin-bottom:1em;">
<summary> Constraint (for using App) </summary>
<div style="margin-left:1em">

1. App **cannot directly combine** reports from different Workspace into a single app
    - Move or copy the **Reports** themselves into a single Workspace using the shared datasets.
</div>
</details>
