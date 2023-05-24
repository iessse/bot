import re

import requests

import base64

from telegram import Update, ForceReply

from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext

# 用集合模拟表来存储授权名单，这里只是示例，实际应用中可以使用数据库

authorized_users = {'123456789', '987654321'} 

def start(update: Update, _: CallbackContext) -> None:

    """发送欢迎消息给已授权用户"""

    user_id = str(update.effective_user.id)

    if user_id in authorized_users:

        update.message.reply_text('欢迎使用！\n示例：/query吴昊452623199001164212')

    else:

        update.message.reply_text('未授权')

def query(update: Update, _: CallbackContext) -> None:

    """处理 /query 命令"""

    user_id = str(update.effective_user.id)

    if user_id not in authorized_users:

        update.message.reply_text('未授权')

        return

    # 提取命令参数

    command_args = update.message.text.strip()[6:]

    if not command_args:

        update.message.reply_text('参数缺失')

        return

    # 提取姓名和身份证号

    match_result = re.match(r'([一-龥]{2,3})\s*([1-9]\d{5}(?:18|19|20)\d{2}(?:0[1-9]|10|11|12)(?:0[1-9]|[1-2]\d|30|31)\d{3}[\dXx])', command_args)

    if not match_result:

        update.message.reply_text('参数不合法')

        return

    

    name = match_result.group(1)

    idcard = match_result.group(2)

    # 请求 API 获取照片

    url = f'https://wx.rd-ic.com:8093/sscard-server/getGAPhoto.html?idcard={idcard}&name={name}'

    response = requests.get(url)

    if '获取失败' in response.text:

        update.message.reply_text('二要素不对或者空')

        return

    # 解码照片并回复给用户

    photo_data = base64.b64decode(response.json()['photo'])

    update.message.reply_photo(photo_data)

def main() -> None:

    """启动机器人"""

    # TODO: 将 YOUR_TOKEN 替换为自己的 Telegram Bot API Token

    updater = Updater("YOUR_TOKEN")

    dispatcher = updater.dispatcher

    # 添加消息处理函数，分别处理 /start 和普通消息

    dispatcher.add_handler(CommandHandler("start", start))

    dispatcher.add_handler(CommandHandler("query", query))

    dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, start))

    # 启动机器人

    updater.start_polling()

    # 运行直到按下 Ctrl-C 或程序结束

    updater.idle()

if __name__ == '__main__':

    main()

