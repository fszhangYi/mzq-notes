使用openai做一些复杂工作往往有奇效。笔者最近被安排了一项统计组员周报的任务，这么复杂的任务我觉得凭自身能力来做的话还是太浪费时间了，于是我想到使用openai来完成。

具体来说就是需要将组员每天发送的日报格式化然后翻译成英文，这种工作对于ai来说简直不要太简单，解决这个问题的思路如下所示：

```js
// 引入js库
import OpenAI from "openai";
// 构建处理函数
const formatContent = async (content: any) => {
  const openai = new OpenAI({
    apiKey: '你的apikey',
    baseURL: '你的baseURL',
    dangerouslyAllowBrowser: true, // 这个需要设置为true否则在浏览器环境下不让使用
  });

  const completion = await openai.chat.completions.create({
    messages: [{ role: "system", content: `
    请将下面的日报内容格式化成markdown格式之后返回,返回格式应该是英文，并且不要有任何多余的文字：
    ${content}
    ` }],
    model: "gpt-4",
  });
  return completion.choices[0] as unknown as string;
}
```

调用此函数之后，等待openai的处理结果即可。