# week04-exam04 - هفته‌ی چهارم، تمرین چهارم، تحلیل بهینه‌ی سفارش‌ها
## تحلیل بهینه‌ی سفارش‌ها

کوئری‌های شما باید روی MySQL قابل اجرا باشند.

یکی از تیم‌های فنی گروه اسنپ قصد دارد برخی از کوئری‌های خود که مربوط به بخش سفارش‌ها هستند را با استفاده از ایندکس‌ها سریع‌تر کند. به آن‌ها در این کار کمک کنید.

## جزئیات پروژه
داده‌های سؤال را از این [لینک](https://quera.org/course/assignments/54252/download_problem_initial_project/181946/) دانلود کنید.

جدولی با نام orders برای نگه‌داری اطلاعات سفارش‌ها موجود است که ساختار آن به شرح زیر است:

    نام ستون	نوع	تعریف
    id	BIGINT(20)	شناسه‌ی سفارش
    user_id	BIGINT(20)	شناسه‌ی کاربر سفارش‌دهنده
    total	DECIMAL(10,2)	مبلغ سفارش
    created_at	DATETIME	زمان ثبت سفارش

## مطلوبات
کوئری‌های SQL خواسته‌شده از شما، موارد زیر است:

1- کوئری ساخت ایندکس جهت بهینه‌سازی حداکثری سرعت محاسبه‌ی مجموع مبلغ سفارش‌ها در سال ۲۰۲۰:

    SELECT SUM(total)
    FROM orders
    WHERE created_at BETWEEN '2020-01-01 00:00:00' AND '2020-12-31 23:59:59'
    

2- کوئری ساخت ایندکس جهت بهینه‌سازی حداکثری سرعت محاسبه‌ی مجموع مبلغ سفارش‌های کاربری با شناسه‌ی ۳۴۵ در سال ۲۰۲۰:

    SELECT SUM(total)
    FROM orders
    WHERE created_at BETWEEN '2020-01-01 00:00:00' AND '2020-12-31 23:59:59'
    AND user_id = 345
    
نکته: تعداد کاربران بسیار بیشتر از حداکثر تعداد سفارش‌های یک کاربر است.

3- مجموع مبلغ سفارش‌ها به ازای هر روز، به‌ترتیب صعودی تاریخ؛ نتیجه به‌ترتیب شامل ستون تاریخ و ستون مجموع مبلغ سفارش‌ها باشد. تاریخ باید به فرمت Y-m-d باشد. همچنین، تاریخ‌هایی بین تاریخ‌های موجود در جدول که در آن‌ها سفارشی ثبت نشده است نیز باید در نتیجه موجود باشند. نام ستون‌های خروجی اهمیت ندارد.
نکته: هر کوئری نمره‌ای جداگانه دارد و اگر کوئری یک قسمت را نتوانستید بزنید، کوئری‌هایی که حل کردید را بفرستید و قسمت آن کوئری را خالی بگذارید.

آن‌چه باید آپلود کنید
پس از طراحی کوئری‌ها، کد خود را در قالب زیر در یک فایل با پسوند .sql آپلود کنید.

   -- Section1
   create index date_int on orders( created_at,total);
-- Section2
  create index user_id_created_at_total_indx on orders( user_id,created_at,total);
-- Section3
WITH RECURSIVE dates (created_at) AS
    (
        SELECT DATE(MIN(created_at)) FROM orders
        UNION ALL
        SELECT created_at + INTERVAL 1 DAY FROM dates
        WHERE created_at + INTERVAL 1 DAY <= (SELECT MAX(created_at) FROM orders)
    )
    SELECT dates.created_at, COALESCE(SUM(total), 0) AS total
    FROM dates
    LEFT JOIN (SELECT total, DATE(created_at) AS created_at FROM orders) o
        ON dates.created_at = o.created_at
    GROUP BY dates.created_at
    ORDER BY dates.created_at;


