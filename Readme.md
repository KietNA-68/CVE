# CVE-2021-39503 (Authenticated Remote Code Execution)
##### Vulnerability param: `site_key`
##### Function: `site_save.php`
##### Effected source code file: `{Source}/admin/site_save.php`
##### Becase of filtered input without `"<, >, ?, =,..."` In `WriteConfig()` function, the attacker can inject php code to `/include/config.cache.php` file. The attacker can append `?>` to close php syntax and adding new php function
![Image](https://user-images.githubusercontent.com/54875703/130347363-253e4b5b-04b9-44d9-8d40-201f8b24de61.png)
![Image](https://user-images.githubusercontent.com/54875703/130347537-9c3c9b36-fcbc-40dc-bdff-83d9e488db4e.png)
##### WriteF() function:
![Image](https://user-images.githubusercontent.com/54875703/130347315-b2427204-26b4-41ae-9e6a-52fa445ecd82.png)
##### PoC:
![Image](https://user-images.githubusercontent.com/54875703/130347687-05de1b51-62bc-43d1-95ef-2249848a81f9.png)
##### In `config.cache.php` file:
![Image](https://user-images.githubusercontent.com/54875703/130347788-5f178509-7b49-40ee-bf17-346ea70d2e75.png)
##### Then back to any `.php` files in `/admin/` directory to execute code:
![Image](https://user-images.githubusercontent.com/54875703/130347838-d487d7f7-2be5-4e3b-93c5-7ade2fc73691.png)

# CVE-2021-39496 (Authenticated Reflected Cross-site Scripting)
##### Vulnerability param: `?filename=`
##### Function: `newfile`, `edit`
##### Effected source code file: `{Source]\application\admin\controller\Filemanager.php`
![Image](https://i.imgur.com/JJrTB7z.png)
##### In function `replace_img()` call `upload()` function to upload file to server, because of lacks sanitization of input data, the attacker can inject malicious script code via `fileName` parameter
##### So i call to Filemanager function in url and inject script into filename parameter 
![Image](https://i.imgur.com/IWWkQFb.png)
##### The script was triggered
![Image](https://i.imgur.com/K5GvOM3.png)

# CVE-2021-39497 (Blind Server-side Request Forgery)
##### Vulnerability param: `source[]`
##### Function: `Uploadify`
##### Effected source code file: `{Source]\application\admin\controller\Uploadify.php`
![Image](https://user-images.githubusercontent.com/54875703/130278413-79bd7c9e-6cbb-4f1c-a82a-db602eb58979.png)
##### In line 233, `catchimage` case post `source[]` parameter that user can control, then call to `saveRemote()` function with `$fieldName` parameter => `saveRemote($config,[value of source parameter])`, Then the `saveRemote()` function will call `get_headers()` function to request and get header of http host
```get_headers() function fetch all the headers sent by the server in the response of an HTTP request```
![Image](https://user-images.githubusercontent.com/54875703/130278519-13f90c07-740d-4f7a-bc1e-4ce7ba814045.png)

#####PoC:

![Image](https://user-images.githubusercontent.com/54875703/130278564-d8ea6431-19e0-424e-9604-d95744906976.png)

# CVE-2021-39499 (Reflected Cross-site Scripting)
##### Vilnerability param: `title`
##### Function: `bind_email`
##### Effected source code file: `{Source]\application\user\controller\Users.php`

##### In `bind_email()` function does not sanitize for `title` parameter
![Image](https://i.imgur.com/qpCvA6K.png)
##### Then `{Source]\template\pc\users\bind_email.htm` will call that paramter in `get_email_code()` function
![Image](https://i.imgur.com/NJExb7n.png)
##### PoC:
![Image](https://i.imgur.com/2k0HuLV.png)

![Image](https://i.imgur.com/eeQZV5M.png)

# CVE-2021-39500 (Directory Traversal)
##### Vilnerability param: `tpldir`, `filename`, `type`, `nid`
##### Function: `ajax_newtpl`
##### Effected source code file: `{Source]\application\admin\controller\Archives.php`
##### Because of lacking of sanitizer of input data in params `tpldir`, `filename`, `type`, `nid`, The attacker can inject dot dot to escape root directory
![Image](https://user-images.githubusercontent.com/54875703/130314686-7827a2ac-8d9f-41f1-b892-e1cf86d86498.png)
![Image](https://user-images.githubusercontent.com/54875703/130314694-f38a640d-3743-4b30-ad02-095a2dacf41c.png)
##### PoC:
![Image](https://user-images.githubusercontent.com/54875703/130314701-a8cabe8e-9e2d-43c2-8c0d-7c995a638a95.png)
##### File `kietna.htm` was created in web root directory via path traversal
![Image](https://i.imgur.com/FFeemNj.png)

# CVE-2021-39501 (Open Redirect)
##### Vilnerability param: `referurl`
##### Function: `logout`
##### Effected source code file: `{Source]\application\user\controller\Users.php`
##### Logout function accepts a user-controlled input that specifies a link to an external site, and uses that link in a Redirect. This simplifies phishing attacks.
![Image](https://user-images.githubusercontent.com/54875703/130326456-1f448474-3b12-4e5e-bb27-c981fb12486a.png)
##### PoC:
```
GET /index.php?m=user&c=Users&a=logout&referurl=https://google.com HTTP/1.1
Host: 172.16.0.12:3333
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:90.0) Gecko/20100101 Firefox/90.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PortalOpenEMR=BKEx0ZLJ9X41gReq-UHNt-aC0jHNPiQLUOf7FXckqCAumudg; OpenEMR=UwreHaTw9iqwJWXqAY3%2CWYkZgvA3wdVmymdC5QqiVC1H2scM; loader=loaded; admin_lang=cn; home_lang=cn; workspaceParam=index%7CArchives; referurl=%2Findex.php%3Fm%3Duser%26c%3DUsers%26a%3Dcentre; ENV_GOBACK_URL=%2Flogin.php%3Fm%3Dadmin%26c%3DArchives%26a%3Dindex_archives%26typeid%3D24%26lang%3Dcn; ENV_LIST_URL=%2Flogin.php%3Fm%3Dadmin%26c%3DArchives%26a%3Dindex_archives%26lang%3Dcn; ENV_IS_UPHTML=0; PHPSESSID=g0e80krlrkdep97u55tu3d2tj2
Upgrade-Insecure-Requests: 1
```
```
HTTP/1.1 302 Found
Date: Sat, 21 Aug 2021 15:17:05 GMT
Server: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/7.3.29
X-Powered-By: PHP/7.3.29
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-cache,must-revalidate
Pragma: no-cache
Set-Cookie: users_id=deleted; expires=Thu, 01-Jan-1970 00:00:01 GMT; Max-Age=0; path=/
Set-Cookie: users_id=deleted; expires=Thu, 01-Jan-1970 00:00:01 GMT; Max-Age=0; path=/
Location: https://google.com
Content-Length: 0
Connection: close
Content-Type: text/html; charset=utf-8
```
