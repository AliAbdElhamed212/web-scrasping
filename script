import requests
from bs4 import BeautifulSoup
import time
import random
import csv

# الأساسيات
base_url = "https://wuzzuf.net/search/jobs/?q=data&a=hpb&page="
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36"
}

page = 1  # نبدأ من الصفحة الأولى
all_jobs = []  # قائمة لتخزين البيانات
max_pages = 3  # حد أقصى لعدد الصفحات

while True:
    url = base_url + str(page)
    print(f"\nجلب البيانات من الصفحة {page} ...")

    # تأخير عشوائي لمنع الحظر
    time.sleep(random.randint(5, 10))

    response = requests.get(url, headers=headers)

    if response.status_code != 200:
        print("فشل في جلب الصفحة، كود الخطأ:", response.status_code)
        break

    soup = BeautifulSoup(response.text, "html.parser")

    # استخراج بيانات الوظائف
    job_cards = soup.select("div.css-1gatmva")  # تحديد الكارت الخاص بكل وظيفة

    if not job_cards:  # إذا لم نجد وظائف، نتوقف
        print("لا توجد وظائف إضافية. توقف البحث.")
        break

    for job in job_cards:
        try:
            job_title = job.select_one("h2 a").text.strip()
            company_name = job.select_one("a.css-17s97q8").text.strip()
            loc = job.select_one("span.css-5wys0k").text.strip() if job.select_one("span.css-5wys0k") else "غير محدد"
            job_type = job.select_one("a.css-n2jc4m").text.strip()
            
            all_jobs.append([job_title, company_name, job_type,loc])
            print(f"- {job_title} | {company_name} | {job_type}| {loc}")
        
        except AttributeError:
            continue  # إذا كان هناك عنصر مفقود، تخطاه

    # التحقق من الوصول إلى الحد الأقصى للصفحات
    if page >= 100:
        print(f"تم الوصول إلى الحد الأقصى للصفحات ({max_pages}). توقف البحث.")
        break

    page += 1  # الانتقال إلى الصفحة التالية

# حفظ البيانات في ملف CSV
file_name = "wuzzuf_jobs.csv"
with open(file_name, "w", newline="", encoding="utf-8") as file:
    writer = csv.writer(file)
    writer.writerow(["job_title", "company_name","job_type", "location"])
    writer.writerows(all_jobs)

print(f"\nwuzzaf تم حفظ {len(all_jobs)} وظيفة في {file_name} بنجاح!")
