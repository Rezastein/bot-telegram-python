### **_source code_**

```
from telegram import Update
from telegram.ext import Application, MessageHandler, ContextTypes, filters
from openpyxl import Workbook, load_workbook
from datetime import datetime
import os

TOKEN = "ISI TOKEN KAMU"
FILE = "NamaFile.xlsx"

def init_excel():
    if not os.path.exists(FILE):
        wb = Workbook()
        ws = wb.active
        ws.append(["Nama Barang", "Harga", "Waktu Input"])
        wb.save(FILE)

async def handle_text(update: Update, context: ContextTypes.DEFAULT_TYPE):
    text = update.message.text

    if "|" not in text:
        await update.message.reply_text("Format salah\nContoh:\nNama Barang | 150000")
        return

    nama, harga = [x.strip() for x in text.split("|", 1)]

    if not harga.isdigit():
        await update.message.reply_text("Harga harus angka\nContoh: Mouse | 150000")
        return

    wb = load_workbook(FILE)
    ws = wb.active

    ws.append([
        nama,
        int(harga),
        datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    ])

    wb.save(FILE)

    await update.message.reply_text(
        f"✅ Data tersimpan\n"
        f"Barang: {nama}\n"
        f"Harga: {int(harga):,}"
    )

init_excel()

app = Application.builder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_text))
app.run_polling()

```
