# pyChatGPT

[![PyPi](https://img.shields.io/pypi/v/pyChatGPT.svg)](https://pypi.python.org/pypi/pyChatGPT)
[![License](https://img.shields.io/github/license/terry3041/pyChatGPT.svg?color=green)](https://github.com/terry3041/pyChatGPT/blob/main/LICENSE)
![PyPi](https://img.shields.io/badge/code_style-black+flake8-blue.svg)

An unofficial Python wrapper for OpenAI's ChatGPT API

## Features

-   [x] Cloudflare's anti-bot protection bypass using `undetected_chromedriver`
-   [x] OpenAI / Google / Microsoft login support (experimental)
-   [x] Captcha solvers support (2Captcha, PyPasser)
-   [x] [Headless machines support](#how-do-i-get-it-to-work-on-headless-linux-server)
-   [x] Proxy support (only without basic auth)

## Getting Started

> Since version 0.3.0, this library is using only the `undetected_chromedriver` to bypass Cloudflare's anti-bot protection. `requests` module is no longer used due to the complexity of the protection. **Please make sure you have [Google Chrome](https://www.google.com/chrome/) / [Chromium](https://www.chromium.org/) before using this wrapper.**

### Installation

```bash
pip install -U pyChatGPT
```

### Usage

#### Obtaining session_token

1. Go to https://chat.openai.com/chat and open the developer tools by `F12`.
2. Find the `__Secure-next-auth.session-token` cookie in `Application` > `Storage` > `Cookies` > `https://chat.openai.com`.
3. Copy the value in the `Cookie Value` field.

![image](https://user-images.githubusercontent.com/19218518/206170122-61fbe94f-4b0c-4782-a344-e26ac0d4e2a7.png)

#### Interactive mode

```bash
python3 -m pyChatGPT
```

#### Import as a module

```python
from pyChatGPT import ChatGPT

session_token = 'abc123'  # `__Secure-next-auth.session-token` cookie from https://chat.openai.com/chat
api = ChatGPT(session_token)  # auth with session token
api = ChatGPT(session_token, conversation_id='some-random-uuid')  # specify conversation id
api = ChatGPT(session_token, proxy='https://proxy.example.com:8080')  # specify proxy
api = ChatGPT(session_token, chrome_args=['--window-size=1920,768'])  # specify chrome args
api = ChatGPT(session_token, moderation=False)  # disable moderation
api = ChatGPT(session_token, verbose=True)  # verbose mode (print debug messages)

# auth with google login
api = ChatGPT(auth_type='google', email='example@gmail.com', password='password')
# auth with microsoft login
api = ChatGPT(auth_type='microsoft', email='example@gmail.com', password='password')
# auth with openai login (captcha solving using speech-to-text engine)
api = ChatGPT(auth_type='openai', email='example@gmail.com', password='password')
# auth with openai login (manual captcha solving)
api = ChatGPT(
    auth_type='openai', captcha_solver=None,
    email='example@gmail.com', password='password'
)
# auth with openai login (2captcha for captcha solving)
api = ChatGPT(
    auth_type='openai', captcha_solver='2captcha', solver_apikey='abc',
    email='example@gmail.com', password='password'
)
# reuse cookies generated by successful login before login,
# if `login_cookies_path` does not exist, it will process logining  with `auth_type`, and save cookies to `login_cookies_path`
# only works when `auth_type` is `openai` or `google`
api = ChatGPT(auth_type='openai', email='example@xxx.com', password='password',
    login_cookies_path='your_cookies_path',
)

resp = api.send_message('Hello, world!')
print(resp['message'])

api.reset_conversation()  # reset the conversation
api.clear_conversations()  # clear all conversations
api.refresh_chat_page()  # refresh the chat page
```


#### Summarization using ChatGPT

```python
from pyChatGPT import ChatGPT

session_token = 'abc123'  # `__Secure-next-auth.session-token` cookie from https://chat.openai.com/chat
api = ChatGPT(session_token)  # auth with session token
api = ChatGPT(session_token, conversation_id='some-random-uuid')  # specify conversation id
api = ChatGPT(session_token, proxy='https://proxy.example.com:8080')  # specify proxy
api = ChatGPT(session_token, chrome_args=['--window-size=1920,768'])  # specify chrome args
api = ChatGPT(session_token, moderation=False)  # disable moderation
api = ChatGPT(session_token, verbose=True)  # verbose mode (print debug messages)

# auth with google login
api = ChatGPT(auth_type='google', email='example@gmail.com', password='password')
# auth with microsoft login
api = ChatGPT(auth_type='microsoft', email='example@gmail.com', password='password')
# auth with openai login (captcha solving using speech-to-text engine)
api = ChatGPT(auth_type='openai', email='example@gmail.com', password='password')
# auth with openai login (manual captcha solving)
api = ChatGPT(
    auth_type='openai', captcha_solver=None,
    email='example@gmail.com', password='password'
)
# auth with openai login (2captcha for captcha solving)
api = ChatGPT(
    auth_type='openai', captcha_solver='2captcha', solver_apikey='abc',
    email='example@gmail.com', password='password'
)
# reuse cookies generated by successful login before login,
# if `login_cookies_path` does not exist, it will process logining  with `auth_type`, and save cookies to `login_cookies_path`
# only works when `auth_type` is `openai` or `google`
api = ChatGPT(auth_type='openai', email='example@xxx.com', password='password',
    login_cookies_path='your_cookies_path',
)

resp = api.send_message("summarize text into 50 words: Alzheimer's disease (AD) is a neurodegenerative disease that usually starts slowly and progressively worsens.[2] It is the cause of 60–70% of cases of dementia.[2][11] The most common early symptom is difficulty in remembering recent events.[1] As the disease advances, symptoms can include problems with language, disorientation (including easily getting lost), mood swings, loss of motivation, self-neglect, and behavioral issues.[2] As a person's condition declines, they often withdraw from family and society.[12] Gradually, bodily functions are lost, ultimately leading to death.[13] Although the speed of progression can vary, the typical life expectancy following diagnosis is three to nine years.")

print(resp['message'])

api.reset_conversation()  # reset the conversation
api.clear_conversations()  # clear all conversations
api.refresh_chat_page()  # refresh the chat page
```

## Frequently Asked Questions

### How do I get it to work on headless linux server?

```bash
# install chromium & X virtual framebuffer
sudo apt install chromium-browser xvfb

# start your script
python3 your_script.py
```

### How do I get it to work on Google Colab?

It is normal for the seession to be crashed when installing dependencies. Just ignore the error and run your script.

```python
# install dependencies
!apt install chromium-browser xvfb
!pip install -U selenium_profiles pyChatGPT

# install chromedriver
from selenium_profiles.utils.installer import install_chromedriver
install_chromedriver()
```

```python
# start your script as normal
!python3 -m pyChatGPT
```

## Insipration

This project is inspired by

-   [ChatGPT](https://github.com/acheong08/ChatGPT)
-   [chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

## Disclaimer

This project is not affiliated with OpenAI in any way. Use at your own risk. I am not responsible for any damage caused by this project. Please read the [OpenAI Terms of Service](https://beta.openai.com/terms) before using this project.

## License

This project is licensed under the GPLv3 License - see the [LICENSE](LICENSE) file for details.
