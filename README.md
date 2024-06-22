*After testing, some IPs will display the following picture when accessing*

<img src="https://image.3001.net/images/20231116/1700131811_6555f3e30a1efc2db761f.png" height="50%" width="50%" /></a>


---
### Demo

[https://chatai.451024.xyz](https://chatai.451024.xyz)

> api

```url
https://openai.451024.xyz
```


```url
https://openai-proxy-api.pages.dev/api
```
***

> New project [WeChat robot based on OpenAI](https://github.com/x-dr/wechat-bot)

***

### The demo site is a public service. If you need large-scale use, please deploy it yourself. The demo site is a bit overwhelmed.

![worker](./docs/img/worker.png)


## Use Cloudflare Worker to transfer api.openai.com

1. Create a new Cloudflare Worker
2. Copy the code in [cf_worker.js](https://cdn.jsdelivr.net/gh/x-dr/chatgptProxyAPI@main/cf_worker.js) and paste it into Worker and deploy it
3. Bind the Worker to a domain name that is not blocked
4. Use your own domain name instead of api.openai.com


**[Detailed Tutorial](./docs/cloudflare_workers.md)**



***


## Use CloudFlare Pages for transit

### 1. Deploy transfer API + Openai API balance query (use Authorization query of sess-xxxx, the validity time is unknown)

> [Official Documentation](https://developers.cloudflare.com/pages)

1. ~~Fork this project~~ Click the [Use this template](https://github.com/x-dr/chatgptProxyAPI/generate) button to create a new code base.
2. Log in to the [Cloudflare](https://dash.cloudflare.com/) console.
3. From the account home page, select `pages`> `Create a project` > `Connect to Git`
4. Select the project repository for your Fork, and in the `Set up builds and deployments` section, leave everything as default.


5. Click `Save and Deploy` to deploy, then click `Continue to project` to see the access domain name


> Replace the official interface `https://api.openai.com` with `https://xxx.pages.dev`

**Demo**

[https://chatai.451024.xyz](https://chatai.451024.xyz)

**[Detailed Tutorial](./docs/cloudflare_pages.md)**

### 2. Only deploy the transit API


**[Detailed Tutorial](./docs/cloudflare_proxy_pages.md)**




## docker deployment (requires overseas vps)

> It seems that sse is not supported so it is not recommended.

<details>

<summary>e.g.</summary>

```bash
docker run -itd --name openaiproxy \
            -p 3000:3000 \
            --restart=always \
           gindex/openaiproxy:latest
```

#### use

*api : http://vpsip:3000/proxy/v1/chat/completions*

```bash
curl --location 'http://vpsip:3000/proxy/v1/chat/completions' \
--header 'Authorization: Bearer sk-xxxxxxxxxxxxxxx' \
--header 'Content-Type: application/json' \
--data '{
   "model": "gpt-3.5-turbo",
  "messages": [{"role": "user", "content": "Hello!"}]
 }'

```

</details>



## 用法


<details>

<summary>JavaScript用fetch</summary>

```javascript
const requestOptions = {
    method: 'POST',
    headers: {
        "Authorization": "Bearer sk-xxxxxxxxxxxx",
        "Content-Type": "application/json"
    },
    body: JSON.stringify({
        "model": "gpt-3.5-turbo",
        "messages": [
            {
                "role": "user",
                "content": "hello word"
            }
        ]
    })
};

fetch("https://openai.1rmb.tk/v1/chat/completions", requestOptions)
    .then(response => response.text())
    .then(result => console.log(result))
    .catch(error => console.log('error', error));
  
```

</details>



<details>

<summary>用python</summary>

```python
import requests

url = "https://openai.1rmb.tk/v1/chat/completions"
api_key = 'sk-xxxxxxxxxxxxxxxxxxxx'

headers = {
  'Authorization': f'Bearer {api_key}',
  'Content-Type': 'application/json'
}

payload = {
  "model": "gpt-3.5-turbo",
  "messages": [
    {
      "role": "user",
      "content": "hello word"
    }
  ]
}

try:
    response = requests.post(url, headers=headers, json=payload)
    response.raise_for_status() # 抛出异常，如果响应码不是200
    data = response.json()
    print(data)
except requests.exceptions.RequestException as e:
    print(f"请求错误: {e}")
except json.JSONDecodeError as e:
    print(f"无效的 JSON 响应: {e}")
```

</details>



<details>
<summary>用nodejs chatgpt库</summary>

[transitive-bullshit/chatgpt-api](https://github.com/transitive-bullshit/chatgpt-api)

```javascript
import { ChatGPTAPI } from 'chatgpt'

async function example() {
  const api = new ChatGPTAPI({
    apiKey: "sk-xxxxxxxxxxxxxx",
  // proxy+/v1
    apiBaseUrl:"https://openai.1rmb.tk/v1"


  })

  const res = await api.sendMessage('Hello World!')
  console.log(res.text)
}

example()

```

</details>



<details>

<summary>查询余额</summary>

```javascript
    const headers = {
      'content-type': 'application/json',
      'Authorization': `Bearer sk-xxxxxxxxxxxxxxxxx`
    }
    // 查是否订阅
    const subscription = await fetch("https://openai.1rmb.tk/v1/dashboard/billing/subscription", {
      method: 'get',
      headers: headers
    })
    if (!subscription.ok) {
      const data = await subscription.json()
      // console.log(data);
      return data
      // throw new Error('API request failed')
    } else {
      const subscriptionData = await subscription.json()
      const endDate = subscriptionData.access_until
      const startDate = new Date(endDate - 90 * 24 * 60 * 60);
      console.log(formatDate(endDate, "YYYY-MM-DD"));
      console.log(formatDate(startDate, "YYYY-MM-DD"));
      const response = await fetch(`https://openai.1rmb.tk/v1/dashboard/billing/usage?start_date=${formatDate(startDate, "YYYY-MM-DD")}&end_date=${formatDate(endDate, "YYYY-MM-DD")}`, {
        method: 'get',
        headers: headers
      })
      
      const usageData = await response.json();
      // 账号类型
      const plan = subscriptionData.plan.id
      console.log(usageData);
      }

```

</details>

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=x-dr/chatgptProxyAPI&type=Date)](https://star-history.com/#x-dr/chatgptProxyAPI&Date)

