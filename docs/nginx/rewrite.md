### rewrite总结

1. 同级别的rewrite是按照顺序执行的，当有一个匹配之后 无论后面跟的是last 还是break 都不会继续寻找其他rewrite。所以注意不是精确度匹配

2. rewrite重写的不包含域名，对于service 和 location中匹配的路径都是完整的路径参数，特别是对于location，注意匹配时不会去掉location的path，如下 rewrite的路径是必须包含/test的才可能比配到的。同理rewrite的结果也和location的path没关系，因为要写/$1 而不是$1

   ```shell
   location /test {
   	rewrite ^/test/(.*) /$1 break;
   }
   ```

   

3. 对于location下的last: 重写后的url会重新匹配location  break 会停止匹配location。 对于server下的rewrite 总是要匹配url的，因此last和break没区别。

4. location中rewrite中不建议使用last，避免出现死循环

5. rewrite的日志打开方式。 rewrite日志会写到error日志中而不是access日志

   ```
   #在server下添加
    error_log  logs/error.log  notice;
    rewrite_log on;
   ```

   

