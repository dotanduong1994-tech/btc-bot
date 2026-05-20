
import requests
import datetime

# ===================== CONFIG =====================
TELEGRAM_TOKEN = "YOUR_BOT_TOKEN"
CHAT_ID = "YOUR_CHAT_ID"

# ===================== GET PRICE =====================
def get_btc_price():
    url = "https://api.coingecko.com/api/v3/simple/price?ids=bitcoin&vs_currencies=usd"
    try:
        response = requests.get(url)
        data = response.json()
        return data["bitcoin"]["usd"]
    except:
        return None

# ===================== SMART DCA LOGIC =====================
def calc_dca_amount(price):
    if price is None:
        return 0

    if price > 90000:
        return 1000
    elif price > 80000:
        return 1500
    elif price > 70000:
        return 2000
    elif price > 60000:
        return 3000
    else:
        return 4000

# ===================== TELEGRAM =====================

TELEGRAM_TOKEN = "8830166228:AAEbkJc60XJYR0Bs5pTtlGbl0LqNomGSbAA"
CHAT_ID = "1187893997"

def send_telegram(msg):
    url = f"https://api.telegram.org/bot8830166228:AAEbkJc60XJYR0Bs5pTtlGbl0LqNomGSbAA/sendMessage"
    try:
        requests.post(url, data={
            "chat_id": CHAT_ID,
            "text": msg
        })
    except:
        print("❌ Lỗi gửi Telegram")


# ===================== LOG FILE =====================
def save_log(msg):
   with open("dca_log.txt", "a", encoding="utf-8") as f:
        f.write(msg + "\n")

# ===================== MAIN =====================
def main():
    now = datetime.datetime.utcnow() + datetime.timedelta(hours=7)  # convert sang giờ VN

    price = get_btc_price()

    if price is None:
        msg = f"❌ Không lấy được giá BTC - {now}"
        print(msg)
        send_telegram(msg)
        return

    amount = calc_dca_amount(price)
    btc = amount / price

    msg = f"""
📅 {now.strftime('%Y-%m-%d %H:%M')} (VN)

📊 BTC Price: ${price:,.0f}
💰 Buy Amount: ${amount:,}
🪙 BTC Bought: {btc:.6f}

🚀 Strategy: Smart DCA
"""

    print(msg)
    send_telegram(msg)
    save_log(msg)



if __name__ == "__main__":
    while True:
        main()  # gửi auto

        check_telegram_commands()  # ✅ thêm dòng này

        print("⏳ Waiting...")
        time.sleep(10)  # check nhanh hơn để bắt command
