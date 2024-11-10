---
cover: /assets/images/cover4.webp
icon: pen-to-square
date: 2024-11-10
category:
  - frontend
tag:
  - typescript
  - crawler
star: true
sticky: true
---

# Use Typescript to write Crawler

## Project Init

创建 package.json 和 tsconfig.json，便捷的命令是`npm init -y`和`tsc --init`，当然你也可以手动创建手动添加配置项


## Superagent


superagent是一个请求代理模块，可以使用它在 node 环境中发送 ajax 请求。

先安装它，`npm install superagent --save`，然后再安装它的.d.ts类型声明文件，`npm install @types/superagent --D`。

我们主要使用它的 superagent.get 方法，会去请求链接，然后返回我们要爬取的内容。

```ts
private async getSourHtml(): Promise<string> {
    const response: superagent.Response = await superagent.get(this._url);
    return response.text;
}
```

## Cheerio


我们需要对爬取到的 html 内容进行分析提取，可以通过**cheerio**来获取 html 内容里的模块组成

```ts
private getRankInfo(html: string): IrankInfo {
    // 将html字符串用cheerio转为dom元素
    this._$ = cheerio.load(html);
    // 文章信息在id为allupdate的ul里
    const rankHtml: cheerio.Cheerio = this._$('.rank_main');
    // console.log('rankHtml', rankHtml.html());
    // 最后要返回的值
    const rankInfo: IrankInfo = { name: '', list: new Array<Irank>() };
    // 标题
    const nameHtml = this._$(rankHtml).find('.rank_banner .rank_i_title_name')?.children();
    rankInfo.name = nameHtml ? nameHtml.text() : '人气排行榜';
    // 榜单清单
    const listHtml = this._$(rankHtml).find('.rank_i_lists .rank_i_p_list');
    listHtml.map((index, value) => {
        if ([0, 2, 4, 5].includes(index)) { // 只收取有点击数的，其他的暂时不作处理
            const name: string = unescapeCode(this._$(value).find('.rank_i_p_tit').text());
            const list: Ibook[] = this.handleBookInfo(this._$(value).find('.rank_i_li'));
            rankInfo.list.push({ name, list });
        }
    });
    return rankInfo;
}
private handleBookInfo(obj: cheerio.Cheerio): Ibook[] {
    const bookArr: Ibook[] = [];
    obj.map((index, value) => {
        let num = '';
        let bookName = '';
        let bookCount = '';
        if (index === 0) {
            const data = this._$(value).children();
            num = unescapeCode(data.eq(0).text());
            bookName = unescapeCode(data.eq(1).children().eq(0).text());
            bookCount = unescapeCode(data.eq(1).children().eq(3).text());
            bookCount = bookCount.replace(/[^0-9]/ig, ''); // 将非数字去掉
        } else {
            const data = this._$(value).children();
            num = unescapeCode(data.eq(0).text());
            bookName = unescapeCode(data.eq(1).children().eq(0).text());
            bookCount = unescapeCode(data.eq(2).text());
        }
        bookArr.push({ num, bookName, bookCount });
    });
    return bookArr;
}
```

## 编译运行

无序列表

- 1. 下载环境
  - vs code
  - node.js
- 111124124
- 451513251

有序列表
1. 122314
2. 111124124
3. 451513251

表格

| 姓名 | 年龄 | 性别 | 爱好 |
| ---- | ---- | ---- | ---- |
| 张三11 | 20   | 男   | 篮球 |
| 李四 | 22   | 女   | 篮球 |
| 王五 | 25   | 男   | 篮球 |