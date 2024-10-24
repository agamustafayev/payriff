
# Payriff API İnteqrasiyası (Node.js)

Bu layihə, Node.js və Axios istifadə edərək Payriff API vasitəsilə sifariş yaratma əməliyyatını göstərir.


## Tələblər

Bu layihəni işlətmək üçün aşağıdakı tələblərə cavab verməlisiniz:

- **Node.js** (>= 12.x)
- **Axios** (HTTP sorğularını göndərmək üçün)

Axios kitabxanasını quraşdırmaq üçün aşağıdakı əmri işlədin:

```bash
npm install axios
```

## İstifadə

 - amount: Ödəniş məbləği (AZN valyutasında).
 - approveURL: Ödənişin uğurla tamamlandığı halda yönləndirilmə linki.Bu URLə Həm GET həmdə POST istəyi atılıt
 - cancelURL: Ödəniş ləğv edildikdə yönləndirilmə linki.Bu URLə Həm GET həmdə POST istəyi atılıt
 -  currencyType:  Valyutanın növü (AZN).
 -  declineURL: Ödənişin rədd edildiyi halda   yönləndirilmə linki (boş ola bilər).Bu URLə Həm GET həmdə POST istəyi atılıt
 -  description: Sifariş haqqında  təsvir (məsələn, məhsulun adı).
 - language: API interfeys dili (AZ).
 - merchant: Satıcı ID'si (Payriff tərəfindən təqdim edilir).

```javascript

const axios = require('axios');

// Sorğu üçün lazım olan məlumatlar
const body = {
    "amount": 10,
    "approveURL": 'https://markup.az/api/payriff-success',
    "cancelURL": 'https://markup.az/api/payriff-cancell',
    "currencyType": "AZN",
    "declineURL": '',
    "description": "Test Məhsulu",
    "language": "AZ"
};

// Payriff API'ya sorğu göndərilməsi
const url = 'https://api.payriff.com/api/v2/createOrder';
const headers = {
    'Content-Type': 'application/json',
    'Authorization': ' Sizin API açarınız . Merchand Olan bölmədə olur' 
};

axios.post(url, {
    ...body,
    merchant: 'Merchand İD niz' 
}, { headers })
    .then(response => {
    const responseData = response.data;
    if (responseData && responseData.payload && responseData.payload.orderId) {
        const orderId = responseData.payload.orderId;
        console.log("Order ID:", orderId);
    } else {
        console.log("Order ID Tapilmadi.");
    }
    })
    .catch(error => {
        // Xəta halında cavabın işlənməsi
        console.error('Sifariş yaradılarkən xəta baş verdi:', error.response ? error.response.data : error.message);
    });
```

## Proses

İlk olaraq Yuxarı Göstərilən kimi Sorğu yaradılır və APİ a göndərilir . APİ dan Gələn cavaba (respons -a) əsasən 

> orderId

götürülür  və bu orderId DB də saxlanılır. Bunu etməkdə məqsəd Müştəri Ödəniş etdiyi halda hansı Order ə ödəniş etdiyini sizin bilmənizdir.


> https://markup.az/api/payriff-success
Bu sizin Success əməliyyatdında OrderİD ni tutaraq istədiyiniz əməliyyatı edə bilərsiz.

```javascript
app.post('/api/payriff-success', (req, res) => { 
const { payload } = req.body; 
	(payload && payload.orderId)
	{ const orderId = payload.orderId;
	 res.json({ message: `Order ID: ${orderId}` });
	  } else 
	  { res.status(400).json({ message: "Order ID Tapilmadi." });
 } });
```
