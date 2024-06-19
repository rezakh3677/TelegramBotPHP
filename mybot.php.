import requests
from telegram import Update, Bot
from telegram.ext import Updater, CommandHandler, CallbackContext

# توکن ربات تلگرام خود را اینجا قرار دهید
TOKEN = '6754748656:AAH95PBZWjrkcWnlOfQP8BkJMtfGMJ8iO-A'

# کلید API Etherscan خود را اینجا قرار دهید
ETHERSCAN_API_KEY = '6754748656:AAH95PBZWjrkcWnlOfQP8BkJMtfGMJ8iO-A'

# تابع برای دریافت اطلاعات قرارداد از Etherscan
def get_contract_details(contract_address):
    url = f'https://api.etherscan.io/api?module=contract&action=getsourcecode&address={contract_address}&apikey={ETHERSCAN_API_KEY}'
    response = requests.get(url)
    data = response.json()
    if data['status'] == '1' and data['message'] == 'OK':
        result = data['result'][0]
        return result
    else:
        return None

# تابع برای دریافت قیمت ارز از CoinGecko
def get_token_price(contract_address):
    url = f'https://api.coingecko.com/api/v3/simple/token_price/ethereum?contract_addresses={contract_address}&vs_currencies=usd'
    response = requests.get(url)
    data = response.json()
    if contract_address.lower() in data:
        return data[contract_address.lower()]['usd']
    else:
        return None

# تابع برای پردازش پیام /start
def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text('Hello! Send me a contract address to get token details.')

# تابع برای پردازش پیام‌های معمولی
def handle_message(update: Update, context: CallbackContext) -> None:
    contract_address = update.message.text.strip()
    details = get_contract_details(contract_address)
    if details:
        name = details.get('ContractName', 'N/A')
        created_at = details.get('ABI', 'N/A')
        platform = 'Ethereum'
        price = get_token_price(contract_address)
        if price is not None:
            price_info = f"Price: ${price}"
        else:
            price_info = "Price information not available."

        message = (
            f"Token Name: {name}\n"
            f"Created At: {created_at}\n"
            f"Platform: {platform}\n"
            f"{price_info}"
        )
    else:
        message = "Invalid contract address or details not available."

    update.message.reply_text(message)

# تابع اصلی برای راه‌اندازی ربات
def main():
    updater = Updater(TOKEN, use_context=True)
    dispatcher = updater.dispatcher

    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(CommandHandler("help", start))
    dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, handle_message))

    updater.start_polling()
    updater.idle()

if name == 'main':
    main()
