# DS201 · Lab 4 · Dịch máy Anh–Việt

**MSSV:** 24521157

Bài thực hành so sánh ba mô hình Encoder–Decoder trên small-PhoMT để dịch từ tiếng Anh sang tiếng Việt. Notebook lưu sẵn output của 15 ô code, gồm kết quả huấn luyện, điểm ROUGE-L, câu dịch mẫu và ba heatmap Bahdanau.

[Mở notebook trên GitHub](ds201-lab-4-translation.ipynb)

## Mô hình và thiết lập

| Bài | Mô hình | Cách giải mã |
| --- | --- | --- |
| 1 | Encoder–Decoder LSTM | Decoder khởi tạo từ trạng thái cuối của encoder. |
| 2 | Bahdanau attention | Dùng trạng thái decoder trước bước cập nhật để tính attention cộng trên câu nguồn. |
| 3 | Luong global-general attention | Dùng trạng thái decoder hiện tại để tính attention và đưa vector kết hợp vào bước kế tiếp. |

Cả ba mô hình dùng **3 lớp LSTM cho mỗi phía**, hidden size và embedding đều bằng **256**. Cấu hình chung gồm dropout 0,3, Adam với learning rate 0,001, batch size 32, teacher forcing 0,5, seed 42 và 10 epoch.

Sau khi lọc các cặp câu có từ 1 đến 50 token ở mỗi phía, dữ liệu còn 18.607 cặp train, 1.901 cặp dev và 1.786 cặp test. Từ điển được xây từ train. Checkpoint được chọn theo ROUGE-L trên dev, rồi đánh giá trên test bằng giải mã greedy. ROUGE-L là F1 trung bình theo câu, thang 0–1, với tokenizer giữ dấu tiếng Việt.

## Kết quả trên Kaggle

| Mô hình | ROUGE-L dev | ROUGE-L test | Số tham số | Giây/epoch |
| --- | ---: | ---: | ---: | ---: |
| LSTM cơ sở | 0,2277 | 0,2336 | 7.500.963 | 103,9 |
| **Bahdanau** | **0,2980** | **0,3126** | 8.025.763 | 143,1 |
| Luong global-general | 0,2946 | 0,3102 | 7.959.971 | 129,6 |

Bahdanau đạt ROUGE-L test cao nhất trong lần chạy này, hơn LSTM cơ sở 0,0790 và hơn Luong 0,0024. Luong huấn luyện nhanh hơn Bahdanau khoảng 13,4 giây mỗi epoch trên Tesla T4.

## Chạy lại trên Kaggle

1. Tải [notebook](ds201-lab-4-translation.ipynb) rồi import vào Kaggle Notebooks.
2. Bật **GPU T4** và **Internet** trong cài đặt notebook.
3. Chọn **Run All**. Notebook cài các thư viện còn thiếu, tải ba tệp small-PhoMT từ nguồn giảng viên và kiểm tra SHA-256 trước khi huấn luyện.
4. Xem biểu đồ, bảng điểm và câu dịch trong notebook. Thư mục `artifacts/` dưới `/kaggle/working/` chứa checkpoint, bảng CSV, biểu đồ và metadata do lần chạy tạo ra.

## Tài liệu tham khảo

- [Bộ chia small-PhoMT dùng trong bài](https://drive.google.com/drive/folders/186OAOuSEYEDVcry7WP5UBdqECXo26QAb)
- [Bahdanau và cộng sự · Neural Machine Translation by Jointly Learning to Align and Translate](https://arxiv.org/abs/1409.0473)
- [Luong và cộng sự · Effective Approaches to Attention-based Neural Machine Translation](https://arxiv.org/abs/1508.04025)
