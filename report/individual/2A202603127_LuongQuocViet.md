# Báo cáo cá nhân — Lương Quốc Việt — 2A202603127

**Lab Ngày 4: Gán keypoint người và khuôn mặt trên CVAT.**

Báo cáo được đối chiếu với mã nguồn, `assignment.json`, hai file ZIP xuất nhãn và ảnh trong `data/` vào ngày 12/11/2026 (GMT+7). Số liệu là số đếm từ bản xuất hiện tại. Những thông tin không có trong repo được ghi rõ là chưa xác minh.

**Lưu ý về phần nhận xét:** mục 3, 4 và 7 được AI hỗ trợ phân tích ảnh và nhãn theo yêu cầu của học viên, chưa phải ghi chép tự review do học viên cung cấp. Theo `RULES.md`, học viên cần tự kiểm tra, xác nhận và viết lại các mục này bằng trải nghiệm thực tế trước khi nộp. Báo cáo không xác nhận những lỗi đã được sửa khi chưa có bản xuất mới.

## 0. Thông tin học viên

- Họ tên: **Lương Quốc Việt**.
- MSSV: **2A202603127**.
- Email: **[Cần bổ sung]**.
- Link repository: [K5-L2A-LuongQuocViet-2A202603127-KeypointPose-Student](https://github.com/23010769-sketch/K5-L2A-LuongQuocViet-2A202603127-KeypointPose-Student).
- Phiên bản CVAT (Help → About): **[Chưa xác minh]**. Tài liệu lớp mô tả bản 2.82, nhưng đây không phải bằng chứng về bản đang chạy trên máy.
- Máy: **Windows 11 · RAM khả dụng cho hệ điều hành khoảng 15,6 GiB · chế độ CVAT đầy đủ/lite chưa xác minh**.
- Bộ ảnh: có [`assignment.json`](../../src/exports/assignment.json), phiên bản kho `d04-k5-v2`.
- Project: `d04-keypoint`.
- Task người: `persons-LuongQuocViet`, task ID 4, job ID 4.
- Task mặt: `faces-LuongQuocViet`, task ID 5, job ID 5.
- Giờ bắt đầu thực hành: **[Cần bổ sung thời gian thực tế]**.
- Giờ kết thúc thực hành: **[Cần bổ sung thời gian thực tế]**.

Dữ liệu gồm 12 ảnh người và 8 ảnh mặt. Đã tính lại danh sách bằng hàm `assignment()` trong `lab.py` với MSSV trên: cả hai danh sách khớp `assignment.json`; danh sách ảnh trong hai ZIP cũng khớp bộ ảnh được giao.

Nguồn ảnh của phiên bản repo này là **COCO 2017**, trong đó ảnh mặt được cắt và phóng to từ ảnh COCO. Thông tin này theo [`src/lab/NGUON_ANH.md`](../../src/lab/NGUON_ANH.md) và `pool.json`, thay cho mô tả Wikimedia Commons trong mẫu báo cáo ban đầu.

## 1. Kiểm tra label (CP0)

Đối chiếu `categories` trong hai file xuất với `src/keypoint_schema.json` và cấu hình label trong `src/lab/labels_d04_keypoint.json`:

| Label | Kiểu trong cấu hình | Số điểm | Tên điểm đúng bảng của lab? | Đúng thứ tự? | Số cạnh |
|---|---|---:|---|---|---:|
| `person` | Skeleton | 17 | Có | Có | 19 |
| `face` | Skeleton | 13 | Có | Có | 13 |

Mẫu `person` trong cấu hình dựng theo người quay mặt về camera: **có**, các điểm `left_*` nằm bên phải khung mẫu. Ví dụ, vai trái có `x = 64`, vai phải có `x = 36` trong SVG của mẫu. Chưa có ảnh chụp label constructor để xác nhận trực tiếp trên giao diện CVAT.

Theo bảng thao tác của lab: rê chuột vào đúng một điểm để điểm sáng lên, bấm `Q` bật/tắt `occluded`, bấm `O` bật/tắt `outside`, hoặc mở menu chuột phải của điểm; không dùng `hidden` thay cho `occluded`. Cần xác nhận thao tác này trên phiên bản CVAT thực tế.

Quy ước đọc nhãn: trái/phải theo người được gán; `v = 2` là nhìn thấy, `v = 1` là bị che nhưng ước lượng chắc, `v = 0` là ngoài ảnh hoặc không xác định được vị trí đáng tin cậy.

## 2. Số lượng đã gán và thời gian (CP1, CP2)

Các cột số skeleton dưới đây được đếm từ annotation thực tế, không lấy số lượng mục tiêu của đề bài thay cho kết quả.

| Nhóm ảnh | Số ảnh | Số skeleton đã gán | Số người dưới 60 px đã bỏ qua | Số poster/gương/màn hình bỏ qua | Thời gian (phút) |
|---|---:|---:|---|---|---|
| `easy` | 5 | 5 | Chưa thống kê | Chưa thống kê | Chưa ghi nhận |
| `occluded` | 3 | 7 | Chưa thống kê | Chưa thống kê | Chưa ghi nhận |
| `back` | 2 | 4 | Chưa thống kê | Chưa thống kê | Chưa ghi nhận |
| `small` | 1 | 2 | Chưa thống kê | Chưa thống kê | Chưa ghi nhận |
| `crowd` | 1 | 3 | Chưa thống kê | Chưa thống kê | Chưa ghi nhận |
| `faces` | 8 | 8 | — | Chưa thống kê | Chưa ghi nhận |
| **Tổng** | **20** | **29** | **Chưa có số đếm thực tế** | **Chưa có số đếm thực tế** | **Chưa ghi nhận** |

Chi tiết ảnh người:

| Nhóm | Ảnh và số skeleton |
|---|---|
| `easy` | `000000196283.jpg`: 1; `000000241837.jpg`: 1; `000000295358.jpg`: 1; `000000314154.jpg`: 1; `000000564304.jpg`: 1 |
| `occluded` | `000000122199.jpg`: 2; `000000189365.jpg`: 2; `000000461389.jpg`: 3 |
| `back` | `000000297227.jpg`: 2; `000000391222.jpg`: 2 |
| `small` | `000000323705.jpg`: 2 |
| `crowd` | `000000381889.jpg`: 3 |

Nhóm mặt có 3 ảnh thẳng, 2 ảnh nghiêng, 1 ảnh kính trong, 1 ảnh kính râm và 1 ảnh tóc; mỗi ảnh có đúng một skeleton `face`.

- Số ảnh người đã xong ở mốc phút 105: **chưa có ghi chép để xác định**; không suy ra được từ ZIP cuối buổi.
- Thời gian thực tế: CP1 **chưa ghi nhận** · CP2 **chưa ghi nhận** · CP3 **chưa ghi nhận**.
- Ảnh tốn thời gian nhất: **chưa xác định** vì không có nhật ký thời gian. Về độ khó quan sát, `000000381889.jpg` có nhiều người chồng lên nhau, tay gần nhau và phần chân bị che/cắt; đây là ảnh cần rà soát kỹ, nhưng không đủ cơ sở khẳng định đã tốn nhiều thời gian nhất.

Bản xuất có **21 skeleton người**, trong khi README nêu mục tiêu 20 người. Script không kiểm được chính xác số người cần gán bằng ảnh gốc, nên chênh lệch này cần kiểm tra lại trên CVAT, chưa thể kết luận skeleton nào thừa.

## 3. Bảng ca khó (CP1, CP2)

Đây là các ca khó nhận diện khi đối chiếu ảnh và bản xuất hiện tại. Cột cuối trình bày nhận xét để học viên xác nhận, không suy đoán lý do đã chọn cờ trong lúc gán. `#n` là thứ tự annotation của cùng ảnh trong JSON, tương ứng cách đánh số của script; không phải ID đối tượng trên CVAT.

| Ảnh | Người/mặt | Điểm | Cờ trong bản xuất | Lựa chọn cần phân biệt | Nhận xét từ ảnh và nhãn |
|---|---|---|---|---|---|
| `000000122199.jpg` | Người #2, bên phải ảnh | `left_shoulder`, `left_elbow` | `v = 2` | `v = 2` / `v = 0` | Cả hai điểm có `x = 480`, đúng mép phải ảnh rộng 480 px. Cần kiểm xem khớp thực nằm trong khung hay đã ra ngoài; khớp ngoài ảnh phải là `v = 0`, không kéo về mép. |
| `000000297227.jpg` | Người #1, người đi xe phía phải | Mắt, vai, hông và chân | Mũi và hai mắt `v = 2`; `left_ear` `v = 0` | Hướng thân / hướng đầu; `v = 2` / `v = 1` / `v = 0` ở mắt | Thân quay khỏi camera, đầu quay sang phải ảnh. Không xác định trái/phải chỉ bằng vị trí mũi; cần lần theo từng bên cơ thể và kiểm mắt xa có thực sự thấy được không. |
| `000000297227.jpg` | Người #2, người đi xe phía trái | `nose`, `left_eye`, `right_eye` | `v = 0` | `v = 1` / `v = 0` | Người quay lưng, không thấy rõ vị trí mũi và mắt. `v = 0` phù hợp khi không thể ước lượng chắc; không bật `v = 1` chỉ để đủ điểm. |
| `000000323705.jpg` | Người #1 và #2 | Đầu, hông, chân và tiêu chí 60 px | Nhiều điểm `v = 0`; còn 3 và 4 điểm `v > 0` | `v = 1` / `v = 0`; giữ / bỏ skeleton | Người ở xa, mờ và bị lan can che. Cần xét kích thước người và độ chắc của từng khớp. Độ trải của các điểm còn nhãn không đại diện đầy đủ chiều cao người. |
| `000000381889.jpg` | Người #1, áo xanh phía trái | `left_shoulder`, `left_hip`; `left_elbow`, `left_wrist` | Vai/hông `v = 1`; khuỷu/cổ tay `v = 0` | `v = 1` / `v = 0` | Vai/hông và các đoạn tay có mức độ che khác nhau. Chỉ giữ `v = 1` khi xác định vị trí chắc từ cấu trúc thân; tay khuất khó tách khỏi người bên cạnh có thể cần `v = 0`. |
| `face_301643.jpg` | Mặt #1, kính râm | 8 điểm `left_eye_*`, `right_eye_*` | Tất cả `v = 2` | `v = 2` / `v = 1` / `v = 0` | Tròng kính tối che mắt. Theo guideline, cần `v = 1` cho các điểm mắt có thể ước lượng chắc theo gọng; không ghi là nhìn thấy trực tiếp. |

## 4. Bảng tự review (CP3)

**Bảng rà soát hỗ trợ, học viên cần xác nhận lại trên job.** Phạm vi gồm 5 ảnh người (`crowd`, `small`, hai ảnh `back`, một ảnh `occluded`) và 3 ảnh mặt, duyệt theo tên file giảm dần trong từng task. Chưa có lịch sử thao tác để xác nhận học viên đã thực hiện CP3 hoặc đã sửa lỗi.

Mã `ok` chỉ xác nhận nội dung cụ thể ở cột ghi chú. Những dòng có ghi “nghi vấn” là trường hợp cần kiểm tra, chưa phải lỗi đã được kết luận.

| Ảnh | Người/mặt | Điểm | Mã lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|---|
| `000000391222.jpg` | Người #2, áo tím phía trái | Mũi, hai mắt | `ok` | Người quay lưng; ba điểm đều `v = 0`, phù hợp việc không nhìn thấy và không xác định chắc vị trí trên ảnh. | Không cần sửa trong phạm vi kiểm này |
| `000000381889.jpg` | Người #1, áo xanh | Vai/hông trái và tay trái bị khuất | `ok` | Bản xuất có phân biệt vai/hông `v = 1` với khuỷu/cổ tay `v = 0`; không mặc định mọi điểm bị che cùng một cờ. Cần học viên xác nhận độ chắc của vị trí ước lượng. | Chưa xác nhận trên job |
| `000000323705.jpg` | Người #2, phía phải ảnh | Toàn skeleton | `extra` | Nghi vấn cần kiểm tiêu chí 60 px. Script báo độ trải 15 px từ nhãn; ảnh cho thấy người bị che bởi lan can, nên chưa đủ cơ sở kết luận người thấp hơn 60 px. | Chưa; chưa kết luận phải xóa |
| `000000323705.jpg` | Người #1, phía trái ảnh | Toàn skeleton | `extra` | Nghi vấn tương tự; script báo độ trải 13 px. Cần kiểm kích thước người, không dùng riêng khoảng cách giữa các điểm `v > 0` để quyết định xóa. | Chưa; chưa kết luận phải xóa |
| `000000297227.jpg` | Người #2, phía trái ảnh | Vai, hông và cờ vùng mặt | `ok` | Các điểm vai/hông trái nằm phía trái ảnh, phù hợp thân quay lưng; mũi và hai mắt đều `v = 0`. | Không cần sửa trong phạm vi kiểm này |
| `000000297227.jpg` | Người #1, phía phải ảnh | Các cặp trái/phải | `lr_swap` | Nghi vấn từ script: mũi và hai mắt `v = 2` nhưng các cặp thân gán như quay lưng. Ảnh có đầu và thân khác hướng; phải kiểm theo giải phẫu trước khi đổi tên. | Chưa; cần xác nhận cả cờ mắt |
| `000000122199.jpg` | Người #2 | `left_shoulder` | `vis_flag` | Điểm `(480, 217.20, 2)` nằm đúng mép phải. Cần kiểm khớp có ra ngoài ảnh hay không trước khi giữ `v = 2`. | Chưa xác nhận |
| `000000122199.jpg` | Người #2 | `left_elbow` | `vis_flag` | Điểm `(480, 271.41, 2)` cũng nằm đúng mép phải; nếu ngoài khung cần bật `outside`. | Chưa xác nhận |
| `000000122199.jpg` | Người #1 | Gối và cổ chân trái/phải | `lr_swap` | Nghi vấn từ script vì hai cặp ngược phía với các cặp còn lại. Cần lần theo từng chân, phân biệt đổi tên với tư thế chân bắt chéo thật. | Chưa xác nhận |
| `face_562485.jpg` | Mặt #1 | Tên mắt trái/phải, khóe trong/ngoài | `ok` | `left_eye_*` nằm bên phải ảnh; khóe ngoài xa sống mũi hơn khóe trong. Tóc không che kín các điểm mắt đang kiểm. | Không cần sửa trong phạm vi kiểm này |
| `face_301643.jpg` | Mặt #1 | 8 điểm mắt | `vis_flag` | Kính râm che mắt nhưng 8 điểm đều `v = 2`; không phù hợp quy ước của lab. Cần đặt `occluded` khi ước lượng chắc. | Chưa; bản ZIP hiện tại vẫn là `v = 2` |
| `face_264124.jpg` | Mặt #1 | Trái/phải mắt, thứ tự mí trên/dưới | `ok` | Mắt trái ở bên phải ảnh; các tọa độ mí trên nằm phía trên mí dưới tương ứng. Chưa thay thế kiểm độ chính xác từng pixel trên CVAT. | Không cần sửa trong phạm vi kiểm này |

Tổng hợp **số dòng theo mã** trong bảng trên, bao gồm cả nghi vấn chưa xác nhận; không phải thống kê lỗi học viên từng mắc hoặc đã sửa:

| `lr_swap` | `vis_flag` | `position` | `missing` | `extra` | `ok` |
|---:|---:|---:|---:|---:|---:|
| 2 | 3 | 0 | 0 | 2 | 5 |

Các số 0 ở `position` và `missing` chỉ có nghĩa chưa ghi nhận dòng thuộc hai mã đó trong phạm vi này, không xác nhận toàn bộ bài không có lỗi vị trí hoặc thiếu người.

Điểm còn cần cân nhắc `v = 1` / `v = 0`: tay khuất của người áo xanh trong ảnh `000000381889.jpg` và các khớp mờ/bị lan can che ở `000000323705.jpg`. Giữ `v = 1` nếu vị trí suy ra chắc từ các đoạn chi lân cận; chọn `v = 0` nếu nhiều vị trí đều có thể đúng. Đây là tiêu chí để kiểm lại, chưa phải lý do lựa chọn do học viên ghi trong buổi lab.

## 5. Kiểm file xuất và kết quả tự kiểm (CP4)

Hai ZIP đều chỉ chứa `annotations/person_keypoints_default.json`, không kèm ảnh gốc. Tên điểm, thứ tự và số cạnh khớp schema. Tất cả annotation người có 51 số, mặt có 39 số; `num_keypoints` khớp số bộ ba có `v > 0`.

Kiểm trực tiếp một số annotation trong JSON:

| File | Đối tượng kiểm | Số phần tử `keypoints` | Cờ quan sát trong JSON | `num_keypoints` khớp? |
|---|---|---:|---|---|
| `LuongQuocViet_persons.zip` | `000000189365.jpg`, #1, annotation ID 3 | 51 | `left_eye`: `(280, 130, 1)`; `left_ear`: `(264.52, 136.20, 0)`; hai cổ chân cũng `v = 0` | Có: 14 = 13 điểm `v = 2` + 1 điểm `v = 1` |
| `LuongQuocViet_persons.zip` | `000000381889.jpg`, #1, annotation ID 13 | 51 | Vai/hông trái `v = 1`; 7 điểm `v = 0` | Có: 10 = 8 điểm `v = 2` + 2 điểm `v = 1` |
| `LuongQuocViet_faces.zip` | `face_301643.jpg`, #1, annotation ID 5 | 39 | Cả 13 điểm `v = 2`; không có mẫu `v = 0/1` trong file mặt để đối chiếu | Có: 13; tuy nhiên cờ mắt kính râm cần sửa theo mục 4 |

Theo quy ước CVAT/COCO của lab, `occluded` tương ứng `v = 1`, `outside` tương ứng `v = 0`. Bản xuất người có 9 điểm `v = 1` và 82 điểm `v = 0`. Cả 82 điểm `v = 0` vẫn mang tọa độ khác `(0, 0)`, ví dụ tai trái ở bảng trên; script coi đây là cách xuất được chấp nhận và bỏ qua tọa độ khi `v = 0`. Chưa đối chiếu trực tiếp từng cờ trong giao diện CVAT với JSON.

Hai job đã chuyển sang completed trên CVAT? **Chưa xác minh được từ file xuất.** Cần kiểm tại [job người](http://localhost:8080/tasks/4/jobs/4) và [job mặt](http://localhost:8080/tasks/5/jobs/5).

Lệnh đã chạy từ thư mục gốc:

```powershell
python -X utf8 src/check_submission.py --persons src/exports/LuongQuocViet_persons.zip --faces src/exports/LuongQuocViet_faces.zip
```

Phần tóm tắt nguyên văn từ script:

```text
----- TÓM TẮT (dán vào báo cáo mục 5) -----
- File person: LuongQuocViet_persons.zip
  Số ảnh: 12 · số skeleton `person`: 21 · mỗi annotation 51 số
  Skeleton theo ảnh: 000000122199=2, 000000189365=2, 000000196283=1, 000000241837=1, 000000295358=1, 000000297227=2, 000000314154=1, 000000323705=2, 000000381889=3, 000000391222=2, 000000461389=3, 000000564304=1
  Cờ v: v=2 266 (75%) · v=1 9 (3%) · v=0 82 (23%)
  num_keypoints trung bình: 13.1
- File face: LuongQuocViet_faces.zip
  Số ảnh: 8 · số skeleton `face`: 8 · mỗi annotation 39 số
  Skeleton theo ảnh: face_147624=1, face_186226=1, face_232598=1, face_264124=1, face_301643=1, face_53580=1, face_562485=1, face_575921=1
  Cờ v: v=2 104 (100%) · v=1 0 (0%) · v=0 0 (0%)
  num_keypoints trung bình: 13.0
- Ghi chú: 82 điểm v = 0 vẫn mang tọa độ cũ (CVAT 2.x xuất như vậy; chuẩn COCO ghi 0, 0). Không phải lỗi: điểm v = 0 bị bỏ qua khi huấn luyện và khi chấm.
- Số [LỖI]: 0 · số [CẢNH BÁO]: 6
Kết quả: ĐẠT (đọc lại từng cảnh báo ở trên)
```

Toàn bộ đầu ra được lưu tại [`check_submission.txt`](check_submission.txt). Các phần trăm ở trên được script làm tròn độc lập nên tổng có thể khác 100%.

Sáu cảnh báo còn trong bản xuất:

| # | Ảnh/đối tượng | Nội dung cảnh báo | Hướng xử lý |
|---:|---|---|---|
| 1 | `000000122199.jpg` #1 | Cặp gối, cổ chân ngược phía so với các cặp khác | Kiểm từng chân và tư thế bắt chéo; chưa đủ cơ sở bỏ qua cảnh báo. |
| 2 | `000000122199.jpg` #2 | `left_shoulder` sát mép phải | Đối chiếu khớp thật; ngoài ảnh thì bật `outside`. |
| 3 | `000000122199.jpg` #2 | `left_elbow` sát mép phải | Xử lý tương tự vai trái. |
| 4 | `000000297227.jpg` #1 | Thấy mũi/hai mắt theo cờ nhưng thân có trái/phải như người quay lưng | Kiểm riêng hướng đầu và thân, cờ mắt và tên các bên; chưa quyết định giữ hay sửa. |
| 5 | `000000323705.jpg` #1 | Độ trải theo chiều cao 13 px, dưới 60 px | Kiểm chiều cao người thực; cảnh báo chỉ dùng điểm có nhãn và bbox xuất. |
| 6 | `000000323705.jpg` #2 | Độ trải theo chiều cao 15 px, dưới 60 px | Kiểm tương tự; không tự động xóa người bị che một phần. |

Chưa có lý do đã được học viên xác nhận để giữ nguyên cả sáu cảnh báo. Chưa sửa JSON hay ZIP bằng tay. Nếu cần thay nhãn, phải sửa trên CVAT rồi xuất lại.

Qua đọc `check_submission.py`, kết quả **ĐẠT** xác nhận các kiểm tra định dạng không có lỗi; script không có ground truth, không tính OKS và không xác nhận mọi điểm đúng vị trí. Nó cũng không nhìn được ảnh để nhận ra kính râm. Vì vậy file mặt không có cảnh báo vẫn có lỗi cờ ở `face_301643.jpg`.

## 6. Ảnh chụp màn hình

Thư mục `report/individual/images/` hiện chỉ có `.gitkeep`, **chưa có bốn ảnh minh chứng**. Cần chụp từ job/giao diện thực tế sau khi kiểm và sửa nhãn, cắt vùng cần thiết rồi đặt đúng tên:

| File cần bổ sung | Nội dung | Nguồn cần ghi dưới ảnh |
|---|---|---|
| `images/1_label_person.png` | Label constructor của `person`, thấy đủ 17 tên điểm và cạnh nối | Cấu hình label của lab trên CVAT |
| `images/2_nguoi_quay_lung.png` | Người quay lưng đã gán, thấy tên điểm và cờ; có thể chọn `000000297227.jpg` hoặc `000000391222.jpg` sau khi kiểm | COCO 2017 |
| `images/3_mat_phong_to.png` | Mặt đủ 13 điểm, phóng to vùng mắt; nếu chọn kính râm cần chụp sau khi đặt đúng `occluded` | COCO 2017, ảnh mặt cắt từ ảnh gốc |
| `images/4_json_export.png` | JSON của một người, thấy mảng `keypoints` và `num_keypoints` | Nhãn xuất từ CVAT trên ảnh COCO 2017 |

<!-- Bỏ dấu chú thích từng dòng sau khi đã có ảnh thật tương ứng.
![Label person](images/1_label_person.png)
![Người quay lưng](images/2_nguoi_quay_lung.png)
![Khuôn mặt phóng to](images/3_mat_phong_to.png)
![JSON export](images/4_json_export.png)
-->

## 7. Tự đánh giá

**Nhận xét hỗ trợ từ dữ liệu hiện có; học viên cần viết lại và xác nhận theo trải nghiệm của mình.**

1. **Lỗi nổi bật và cách phòng tránh**

   Vấn đề xác định rõ qua ảnh là cờ visibility của 8 điểm mắt sau kính râm ở `face_301643.jpg`: nhãn đang là `v = 2`, trong khi guideline yêu cầu `v = 1` nếu có thể ước lượng chắc. Ngoài ra còn có các nghi vấn về trái/phải và điểm sát mép ở ảnh người. Không có nhật ký sửa lỗi nên chưa thể kết luận loại lỗi học viên mắc nhiều nhất trong buổi. Cách phòng tránh phù hợp là rà cờ riêng sau khi đặt tọa độ, rồi kiểm lần lượt hướng thân, hướng đầu và từng chuỗi vai–khuỷu–cổ tay, hông–gối–cổ chân.

2. **Quy tắc có thể bổ sung vào guideline**

   Với người có đầu quay khác hướng thân, cần kiểm hướng thân để xác định trái/phải của tay, hông và chân; kiểm hướng đầu riêng để xác định điểm mắt/tai nào thực sự thấy. Không dùng điều kiện “có mũi và hai mắt” để tự động kết luận toàn thân quay mặt. Ví dụ cần kiểm là người phía phải trong `000000297227.jpg`. Với ảnh bị che nhiều, cũng cần phân biệt chiều cao người với độ trải của các điểm còn `v > 0` khi áp dụng ngưỡng 60 px.

3. **Nhãn đi vào model và tác động của lỗi**

   Với pipeline heatmap mô tả trong tài liệu lab, mỗi điểm có `v > 0` tạo mục tiêu quanh tọa độ được gán; `v = 0` được bỏ qua trong loss. Các cặp trái/phải còn được đổi tên khi lật ảnh. Nếu xác nhận một cặp gối bị đổi tên rồi sửa đúng, thay đổi đó sẽ đưa đỉnh heatmap về đúng kênh khớp và tránh tạo mục tiêu sai sau phép lật ảnh. Bản xuất hiện tại chưa chứng minh lỗi đổi tên nào đã được sửa, nên đây là phân tích tác động, không phải lịch sử sửa bài.

   Riêng đổi cờ kính râm từ `v = 2` sang `v = 1` giúp ghi đúng trạng thái bị che. Trong pipeline đặt cùng trọng số cho `v = 1` và `v = 2` như tài liệu lab, chỉ đổi cờ này mà giữ nguyên tọa độ không làm thay đổi mục tiêu heatmap hoặc trọng số loss; ảnh hưởng cụ thể còn phụ thuộc cách mô hình sử dụng visibility.

## 8. Khó khăn và lỗi kỹ thuật

Về nhãn, các tình huống cần kiểm kỹ là người quay lưng nhưng xoay đầu, khớp sát biên ảnh, người nhỏ bị lan can che và mắt sau kính râm. Những tình huống này đòi hỏi kết hợp quan sát ảnh với cờ trong JSON; chỉ chạy script chưa đủ xác nhận chất lượng.

Về kỹ thuật, lần kiểm file hiện tại chạy thành công bằng Python 3.14.3, mã thoát 0. Khi lập báo cáo chưa đọc được thông tin từ `http://localhost:8080/api/server/about`, nên chưa xác minh phiên bản CVAT và trạng thái completed của job. Đây là hạn chế của lần rà soát, không đủ cơ sở kết luận CVAT đã gặp sự cố trong buổi thực hành.

Repo chưa có nhật ký hoặc ảnh chụp lỗi hệ thống trong buổi; không tự ghi nhận mất dữ liệu, lỗi export hay thời gian xử lý khi chưa có bằng chứng.

Các việc còn cần hoàn tất trước khi nộp:

- Học viên tự xác nhận và viết lại mục 3, 4, 7 theo quy định của bài.
- Kiểm/sửa cờ mắt kính râm và xử lý từng cảnh báo ở mục 5 trên CVAT; xác minh chênh lệch 21 skeleton so với mục tiêu 20 người.
- Xuất lại hai ZIP sau khi sửa, chạy lại script và cập nhật phần tóm tắt.
- Bổ sung email, phiên bản CVAT, chế độ chạy, thời gian thực tế và số đối tượng đã bỏ qua.
- Bổ sung bốn ảnh chụp màn hình; xác nhận hai job completed.

## Bonus (nếu làm)

Chưa có file xuất bonus hoặc ghi chép thử model trong repo được rà soát, nên báo cáo **không ghi nhận hoàn thành B1, B2 hay B3**.

## 9. Tài liệu tham khảo và công cụ AI đã dùng

1. [`RULES.md`](../../RULES.md), [`CHECKPOINTS.md`](../../CHECKPOINTS.md), [`RUBRIC.md`](../../RUBRIC.md), [`SUBMISSION.md`](../../SUBMISSION.md): quy định, checkpoint và yêu cầu nộp.
2. [`docs/GUIDELINE.md`](../../docs/GUIDELINE.md): bộ điểm, trái/phải, visibility và mã lỗi.
3. [`docs/CVAT_CHEATSHEET.md`](../../docs/CVAT_CHEATSHEET.md): thao tác từng điểm và cách xuất file.
4. [`docs/COCO_KEYPOINTS.md`](../../docs/COCO_KEYPOINTS.md): bộ ba `(x, y, v)`, `num_keypoints`, heatmap, loss và OKS.
5. [`lab.py`](../../lab.py), [`src/check_submission.py`](../../src/check_submission.py), [`src/keypoint_schema.json`](../../src/keypoint_schema.json): cách chia bộ ảnh và phạm vi kiểm tra file.
6. [`src/lab/NGUON_ANH.md`](../../src/lab/NGUON_ANH.md), [`src/lab/pool.json`](../../src/lab/pool.json): nguồn và giấy phép từng ảnh. Dữ liệu COCO: Lin et al., *Microsoft COCO: Common Objects in Context* (2014).
7. Minh chứng số liệu: [`assignment.json`](../../src/exports/assignment.json), [`LuongQuocViet_persons.zip`](../../src/exports/LuongQuocViet_persons.zip), [`LuongQuocViet_faces.zip`](../../src/exports/LuongQuocViet_faces.zip), [`check_submission.txt`](check_submission.txt).
8. **Công cụ AI: Codex.** Dùng để đọc mã nguồn, đối chiếu bộ ảnh theo MSSV, đếm nhãn, chạy script và biên tập báo cáo. Theo yêu cầu bổ sung của học viên, AI cũng phân tích ảnh/nhãn để soạn phần nhận xét hỗ trợ ở mục 3, 4, 7; các phần này cần học viên tự xác nhận và viết lại trước khi nộp theo `RULES.md`. Không dùng AI sinh tọa độ keypoint, không sửa annotation trong JSON/ZIP.
