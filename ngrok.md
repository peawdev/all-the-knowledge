# NGROK 

![alt text](image-1.png)

### - ngrok คืออะไร
> คือเครื่องมือที่ใช้ publish application ของเราที่รันบน localhost ขึ้นสู่ public ได้อย่างง่าย โดยไม่ต้องตั้งค่า firewall, router, หรือ public IP 

### - ประโยชน์ของ Ngrok ✅ 
1. ใช้ทดสอบ web application ได้จากภายนอก 
> เปิดให้คนอื่นเข้าถึงเว็บที่คุณพัฒนาบนเครื่องได้ทันที
2. ใช้สำหรับเชื่อมต่อ Webhook จากบริการภายนอก
> เช่น เชื่อมต่อ webhook กับ Line, Facebook เนื่องจากต้องการ URL ที่เข้าถึงได้แบบ public
3. ทดสอบได้โดยไม่ต้อง deploy 
> เช่น ใช้เชื่อมต่อ backend ได้โดยไม่ต้อง deploy
4. debug
> ตรวจสอบ traffic ทั้ง request/response ได้แบบ realtime

#### Ref: https://ngrok.com/docs/what-is-ngrok/#development-and-testing 
_________________

### Quickstart 
- Install
    - Mac OS `brew install ngrok`
    - Windows `choco install ngrok`
- เชื่อมต่อ Account
    - `ngrok config add-authtoken <TOKEN>`
- publish app
    - `ngrok http http://localhost:8080`
    - เมื่อรันเสร็จจะเห็น Console ใน terminal

    ```
    ngrok                                                                   (Ctrl+C to quit)

    Session Status                online
    Account                       inconshreveable (Plan: Free)
    Version                       3.0.0
    Region                        United States (us)
    Latency                       78ms
    Web Interface                 http://127.0.0.1:4040
    Forwarding                    https://84c5df474.ngrok-free.dev -> http://localhost:8080

    Connections                   ttl     opn     rt1     rt5     p50     p90
                                0       0       0.00    0.00    0.00    0.00
    ```
    - จะสามารถนำ URL ในบรรทัด Forwarding ใช้งานได้

_________________
### 📌 หมายเหตุ
- เวอร์ชันฟรี: ใช้ได้ดีสำหรับ dev/test แต่ URL จะเปลี่ยนใหม่ทุกครั้งที่รันใหม่

- เวอร์ชันเสียเงิน (Pro): สามารถตั้ง custom domain ได้, กำหนด subdomain คงที่, มีความปลอดภัยเพิ่มขึ้น

### 📌 ข้อควรรู้
- ถ้าใช้ Ngrok ฟรี ทุกครั้งที่ปิดแล้วเปิดใหม่ URL จะเปลี่ยนใหม่

-  ต้องตั้งค่า Webhook URL (เช่น LINE, Facebook) ต้องอัปเดต URL ให้ตรงกับที่ Ngrok สร้างใหม่ทุกครั้ง

_________________

### Demo กับ Go/Gin

1. เขียน Webhook Server ด้วย Gin
```
// main.go
package main

import (
    "fmt"
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()

    // สร้าง endpoint สำหรับรับ Webhook
    r.POST("/webhook", func(c *gin.Context) {
        var payload map[string]interface{}
        if err := c.BindJSON(&payload); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }

        // แสดง payload ที่รับเข้ามา
        fmt.Printf("Received Webhook: %+v\n", payload)

        // ตอบกลับ
        c.JSON(http.StatusOK, gin.H{"status": "received"})
    })

    r.Run(":3000") // รันที่ localhost:3000
}
```

2. เรียกใช้งาน Ngrok
- เปิดเทอร์มินัลแล้วรันคำสั่ง จะได้ URL
``` 
ngrok http 3000 
```

3. ทดสอบ Webhook
- ทดสอบโดยการเรียกใช้งาน API
```
POST: https://abc123.ngrok.io/webhook
Body: {
    "message": "Hello World"
}
```

4. ผลลัพธ์ใน terminal
```
Received Webhook: map[message:Hello World]
```

#### Ref: https://devhub.in.th/blog/ngrok-test-local-website 
