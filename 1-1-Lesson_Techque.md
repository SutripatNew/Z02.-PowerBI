<img src="picture\images-0.png" alt="alt text" width="100">

<br>


# วิธีการใช้ Parameter [Transform]
> ✍️ มีประโยชน์เวลาเราทำ PowerBI แล้วหากลอง Test ใช้ DB_DEV  
> → พอขึ้นระบบ ใช้ DB_PRD ไม่ต้องมาเปลี่ยนอะไรใหม่ แค่ปรับ Parameter อย่างเดียว
<details>
<summary> ก่อนสร้าง Parameter </summary>
<div style="margin-left:1em">

`= Sql.Databases("10.1.1.124")`

<img src="picture\Techque\image-18.png" width="500">  

`= TNTL_PUR_DEV{[Schema="dbo",Item="VW_FACT_MS_FORM"]}[Data]`

<img src="picture\Techque\image-19.png" width="500"> 
</div>
</details>

<details>
<summary> หลังสร้าง Parameter </summary>
<div style="margin-left:1em">

<img src="picture\Techque\image-20.png" width="300">   

`= Sql.Database("10.1.1.124", "" &DB& "")` **<< Database ลบ s**

<img src="picture\Techque\image-21.png" width="500"> 

`= Source{[Schema="dbo",Item="VW_FACT_MS_FORM"]}[Data] **<< Source**

<img src="picture\Techque\image-22.png" width="500">
</div>
</details>