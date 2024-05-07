<h2 style="" id="%E4%B8%80%E3%80%81redis%E7%BC%93%E5%AD%98%E6%9B%B4%E6%96%B0%E7%AD%96%E7%95%A5"><span
        style="font-size: 18px; color: rgb(17, 24, 39)">一、redis缓存更新策略</span></h2>
<p style="">缓存更新是Redis为了节约内存而设计出来的一个东西，主要是因为内存数据宝贵，当我们向Redis插入太多数据，此时就可能会导致缓存中的数据过多，所以Redis会对部分数据进行更新，或把他叫为淘汰更合适。</p>
<ol>
    <li>
        <p style=""><strong>自动淘汰</strong>：</p>
        <ul>
            <li>
                <p style="">当Redis内存达到<code>max-memory</code>限制时，启动自动淘汰机制。</p>
            </li>
            <li>
                <p style="">可配置多种淘汰策略（如LRU、TTL、LFU等），移除不重要或即将过期的数据。</p>
            </li>
        </ul>
    </li>
    <li>
        <p style=""><strong>超时剔除</strong>：</p>
        <ul>
            <li>
                <p style="">为键设置<strong>TTL</strong>（过期时间），超时后Redis自动删除，释放空间。</p>
            </li>
        </ul>
    </li>
    <li>
        <p style=""><strong>主动更新</strong>：</p>
        <ul>
            <li>
                <p style="">针对数据库更新导致的缓存与数据不一致问题，手动调用方法删除指定缓存。</p>
            </li>
            <li>
                <p style=""><strong>注意</strong>：需要确保后续读取请求获取最新数据，保持数据一致性。</p>
            </li>
        </ul>
    </li>
</ol>
<div style="overflow-x: auto; overflow-y: hidden;">
    <table style="width: 400px">
        <colgroup>
            <col style="width: 100px">
            <col style="width: 100px">
            <col style="width: 100px">
            <col style="width: 100px">
        </colgroup>
        <tbody>
            <tr style="height: 60px;">
                <th colspan="1" rowspan="1" colwidth="100">
                    <p style="">内容</p>
                </th>
                <th colspan="1" rowspan="1" colwidth="100">
                    <p style="">概述</p>
                </th>
                <th colspan="1" rowspan="1" colwidth="100">
                    <p style="">一致性</p>
                </th>
                <th colspan="1" rowspan="1" colwidth="100">
                    <p style="">维护成本</p>
                </th>
            </tr>
            <tr style="height: 60px;">
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">内存淘汰</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">不用自己维护，利用Redis的内存淘汰机制。当内存不足时自动淘汰部分数据。下次查询时更新缓存。</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">差</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">无</p>
                </td>
            </tr>
            <tr style="height: 60px;">
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">超时剔除</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">给缓存数据添加TTL时间，到期后自动删除缓存。下次查询时更新缓存。</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">一般</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">低</p>
                </td>
            </tr>
            <tr style="height: 60px;">
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">主动更新</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">编写业务逻辑，在修改数据库的同时，更新缓存。</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">好</p>
                </td>
                <td colspan="1" rowspan="1" colwidth="100">
                    <p style="">高</p>
                </td>
            </tr>
        </tbody>
    </table>
</div>
<p style=""><strong>业务场景</strong>：</p>
<ul>
    <li>
        <p style="">低一致性需求：使用内存淘汰机制。例如店铺类型的查询缓存。</p>
    </li>
    <li>
        <p style="">高一致性需求：主动更新，并以超时剔除作为兜底方案。例如店铺详情查询的缓存。</p>
    </li>
</ul>
<p style=""></p>
<h2 style=""
    id="%E4%BA%8C%E3%80%81%E6%95%B0%E6%8D%AE%E5%BA%93%E7%BC%93%E5%AD%98%E4%B8%8D%E4%B8%80%E8%87%B4%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88">
    <span fontsize="" color="rgb(17, 24, 39)" style="color: rgb(17, 24, 39)">二、数据库缓存不一致解决方案</span></h2>
<p style="">当数据库更新后，若缓存未能同步，会导致用户看到过时数据，引发业务逻辑错误、用户体验下降及系统稳定性问题。一般采用以下三种策略：</p>
<ol>
    <li>
        <p style=""><strong>Cache Aside Pattern 人工编码方式，双写方案</strong>：</p>
        <ul>
            <li>
                <p style="">应用程序在更新数据库后，立即手动更新或删除对应缓存。</p>
            </li>
            <li>
                <p style=""><strong>优点</strong>：实现简单，灵活控制。适用于读多写少场景。</p>
            </li>
            <li>
                <p style=""><strong>注意</strong>：并发更新可能导致的缓存问题，如击穿、雪崩，需采取相应防范措施。</p>
            </li>
        </ul>
    </li>
    <li>
        <p style=""><strong>Read/Write Through Pattern 系统级集成</strong>：</p>
        <ul>
            <li>
                <p style="">使用中间件或服务层自动同步数据库与缓存，所有读写操作均通过该层进行。</p>
            </li>
            <li>
                <p style=""><strong>优点</strong>：简化开发，自动保证一致性。适用于对一致性要求高的场景。</p>
            </li>
            <li>
                <p style=""><strong>注意</strong>：可能增加系统复杂度，对某些复杂查询或数据模型需定制支持。</p>
            </li>
        </ul>
    </li>
    <li>
        <p style=""><strong>Write Behind Caching Pattern 异步缓写，最终一致性</strong>：</p>
        <ul>
            <li>
                <p style="">应用程序仅更新缓存，后台任务异步批量写入数据库，实现最终一致性。</p>
            </li>
            <li>
                <p style=""><strong>优点</strong>：大幅提升写入性能，适合大量写入且能容忍短暂不一致的场景。</p>
            </li>
            <li>
                <p style=""><strong>注意</strong>：确保可靠的异步处理机制，应对读取“临时”数据及系统故障导致的任务积压或丢失。</p>
            </li>
        </ul>
    </li>
</ol>
<p style=""></p>
<p style=""></p>