# 03 - AI Log & Reflection

## 1. Toi da dung AI de lam gi?

Trong bai lab nay, toi dung AI nhu mot thought-partner de brainstorm cac pain point van hanh cua Vingroup. AI giup toi mo rong danh sach bai toan tu nhung y tuong chung chung thanh cac workflow cu the hon, vi du: Xanh SM xu ly su co pin, Vinhomes phan loai phan anh cu dan, Vinmec tao tom tat xuat vien.

Toi cung dung AI de stress-test y tuong san pham. Khi toi de xuat dung LLM cho bai toan dieu huong tai xe den tram sac, AI giup toi nhin ra cac rui ro: de xuat tram qua xa khi pin sap can, sai loai cong sac, hoac tu dong gui tin cho tai xe ma khong co dieu phoi vien duyet.

Ngoai ra, AI ho tro toi viet system prompt va adversarial test cases cho file `prompt_prototype.py`. Cac test nay co tinh tan cong ranh gioi, vi du yeu cau bo qua tag `[DRAFT_ONLY]` hoac yeu cau gui tai xe den tram sac 8km khi pin chi con 2%.

## 2. AI da sai hoac thieu o dau?

AI ban dau co xu huong de xuat giai phap qua lon, gan voi "agent tu dong dieu phoi" thay vi bat dau bang mot LLM feature hep. Cach de xuat nay co rui ro vi bai toan lien quan den an toan van hanh tren duong. Neu AI tu dong quyet dinh va gui lenh, mot loi nho co the lam tai xe het pin giua duong hoac gay cham tre don khach.

Mot diem sai nua la AI doi khi dua metric nghe co ve hay nhung chua gan voi baseline. Vi du AI de xuat "tang hai long khach hang 30%" nhung khong noi cach do, nguon du lieu, hay thoi gian do. Metric nay kho kiem chung hon so voi metric van hanh truc tiep nhu "giam thoi gian xu ly tu 15 phut xuong duoi 3 phut".

AI cung de xuat rule-based qua don gian cho mot so truong hop ngon ngu tu nhien. Voi phan anh cu dan Vinhomes, neu chi dung keyword rule thi de nham khi cu dan viet dai, viet tat, hoac noi nhieu van de trong mot ticket. Trong truong hop do, LLM co ich hon o buoc doc hieu va tom tat, nhung van can rule de route cac category ro rang.

## 3. Toi da sua prompt va ranh gioi nhu the nao?

Toi dieu chinh prompt theo huong "problem first, AI second". Thay vi yeu cau AI tu dong giai quyet toan bo quy trinh, toi gioi han vai tro cua AI la **dispatcher co-pilot**: chi doc du lieu dau vao, tao draft huong dan, va dua ra canh bao cho dieu phoi vien.

Toi them cac ranh gioi bat buoc:

- Moi tin nhan cho tai xe phai bat dau bang `[DRAFT_ONLY]`.
- AI khong duoc tu dong gui tin, khong duoc noi rang da gui tin.
- Neu pin duoi 5% va tram sac xa hon 5km, AI phai tra ve hanh dong `dispatch_mobile_charger`, khong duoc co gang huong dan tai xe di tiep.
- Neu thieu du lieu quan trong nhu muc pin, toa do xe, loai xe hoac danh sach tram sac, AI phai hoi lai dieu phoi vien thay vi tu suy doan.

Sau khi them cac ranh gioi nay, prototype co the xu ly tot hon cac prompt tan cong. Ket luan cua toi la AI huu ich nhat khi duoc dat trong mot workflow co nguoi duyet, metric ro rang va fallback thu cong. Dung AI de tao draft va tang toc quy trinh la hop ly; dung AI de tu dong ra quyet dinh van hanh an toan cao thi chua nen.
