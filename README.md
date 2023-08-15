# Web_Scraping_Tool

I. Install scrapy

1. Open a new terminal in empty director (cd đến folder chứ chương trình)
2. Tạo thư mục môi trường có tên là venv: 
 >python -m venv venv
3. Activate that folder, kích hoạt môi trường ảo:
 >source venv/bin/activate ( for Linux or Unix)  
 >.\venv\Scripts\activate (for Windows)
Trong bước 3 có thể xảy ra lỗi do terminal ko có quyền load các file scripts. Cách sửa như sau:
- Mở powershell dưới quyền admin. Kiểm tra cấu hình Execution Policy hiện tại, gõ lệnh "Get-ExecutionPolicy" --> kết quả trả về "Restricted"
- Để cho phép chạy các tập lệnh, gõ lệnh "Set-ExecutionPolicy RemoteSigned"
- Chọn Y để change policy.
- chạy lại lệnh >.\venv\Scripts\activate trong terminal.
5. Sau khi kích hoạt môi trường ảo xong, cần khôi phục lại policy trong powershell dưới quyền admin để đảm bảo bảo mật. "Set-ExecutionPolicy Restricted"
6. Kiểm tra pip có nằm trong môi trường ảo vừa kích hoạt không.
 >where pip (for window)
 >which pip (Linux)
7. Cài đặt Scrapy
- Cài đặt Rust (https://www.rust-lang.org/tools/install) 
 >python -m pip install --upgrade pip 
 >pip install scrapy
8. Create a new project 
 >scrapy startproject vulnerability
scrapy is going to create this directory for me 
9. generate xem có cái gì trong project vừa được tạo bởi scrapy
 >cd vulnerability
 >scrapy genspider [name of the spider] [the domain that it should go] (>scrapy genspider cve cve.mitre.org)
 --> create thành công spider cve trong module vulnerability.spiders.cve
 
II. Parsing Data with XPath and Scrapy Shell
1. open the terminal (cd to D:\Cousera_PythonAndSQLForDE\Week3_WebScrapingHTML\vulnerability\vulnerability\spiders> )
2. using a feature of scrapy called scrapy shell to scraping the website
- shell feature helps with fast feedback when trying to get the right syntax to parse the HTML document
 >scrapy shell https://cve.mitre.org/data/refs/refmap/source-EXPLOIT-DB.html
 - Nếu bị lỗi urllib3 v2.0 không tương thích với OpenSSL 1.1.1+ thì có thể cài lại urllib tương thích 
 >pip install scrapy urllib3==1.26.6
3. Sau khi sử dụng scrapy shell để quét xong, nó sẽ cung cấp 1 số phương thức availible để xem response. chọn cái phù hợp. Trong trương hợp này, nhập >>>response để xem trong 200 https://cve.mitre.org/data/refs/refmap/source-EXPLOIT-DB.html
4. Sử dụng xpath để xem data.
 - Access to xpath properties by >response.xpath('//table') : nghĩa là cho ra toàn bộ những bản đơn đã quét được trong document --> kết quả trả về một list các bảng tìm được
 - Để kiểm tra số lượng bảng >len(response.xpath('//table')) --> kết quả trả về 5 bảng
5. Xem table đầu tiên
 >response.xpath('//table')[0]
6. extract the one table that has many different rows
 >len(response.xpath('//table').xpath('//tr')) : có tổng cộng bao nhiêu dòng trong tất cả các bảng --> 10732 dòng
 >len(response.xpath('//table')[1].xpath('tr')) : có bao nhiêu dòng trong bản thứ 2 --> 3 dòng
7. Duyệt qua các dòng trong 1 bảng
 >table = response.xpath('//table')[3]
 >table.xpath('tr')
 >table.xpath('tr')[0] : xem dòng đầu tiên
 >child = table.xpath('//tr')[0] : đặt dòng đầu tiên là child
8. Đọc dữ liệu trong dòng đầu tiên của bảng thứ 3 dùng text function trong xpath
 >child.xpath('td//text()') : td viết tắt của text data, dòng này nghĩa là lấy text của thẻ td
 --> có nhiều items trong dòng đầu tiên của bảng thứ 3
9. extract từng item trong dòng đó dùng extract function trong xpath
 >child.xpath('td//text()')[0].extract() : xem item đầu tiên
10. vòng lặp mỗi dòng của bảng thứ 3, in ra item đầu tiên trong mỗi dòng của bảng thứ 3
 >for row in table.xpath('//tr'):
    try:
        print(row.xpath('td//text()')[0].extract())
    except IndexError:
        pass
    nhớ cách cho thẳng hàng
