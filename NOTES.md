# Ghi chú phân tích · Day 04

Họ và tên: Nguyễn Nhựt Linh
MSSV: 2A202603492

## 1. Case được chọn

- ID case: E2
- Câu trả lời của agent: ## Kết quả tra cứu (snapshot: 2026-11-09T03:50:47Z từ cloudflarestatus.com)\n\n### 1. Incident hiện tại liên quan đến cache purge — CÓ\n\n**`wp49bp8bqo6c` — \"Fedramp Customers have Delayed Purges\"** (đang mở, status: *identified*)\n- Component: **CDN Cache Purge** (hiện đang `degraded_performance`; tổng thể Cloudflare ở mức *Partial System Outage*)\n- Tạo: 2026-11-06T17:47Z | Cập nhật cuối: 2026-11-06T17:59Z\n- Nội dung chính thức: *\"Cloudflare has identified a bug with our purge pipeline impacting fedramp customers. Stale content may be served until expired. **Non-fedramp customers are not impacted.**\"*\n- URL nguồn (từ tool): https://www.cloudflarestatus.com/incidents/wp49bp8bqo6c\n\n### 2. Incident lịch sử (đã resolved — không ảnh hưởng hiện tại)\n\n| ID | Phạm vi | Thời điểm resolved | URL nguồn |\n|---|---|---|---|\n| `5tlm9r9j9rpo` | Delay purge tại **Qingdao, Sơn Đông, Trung Quốc** | 2026-10-31T06:57Z | https://www.cloudflarestatus.com/incidents/5tlm9r9j9rpo |\n| `8s6pn9rvof3l` | Delay tối đa ~30 phút cho purge-by-URL (Dashboard/API) đi qua queue **EEUR**, ảnh hưởng cả Always Online | 2026-10-27T16:14Z | https://www.cloudflarestatus.com/incidents/8s6pn9rvof3l |\n\n### 3. Trả lời câu hỏi về website CLB không thuộc FedRAMP\n\n**Không — theo bằng chứng hiện có, website không thuộc FedRAMP không bị ảnh hưởng bởi incident đang mở.** Đây là khẳng định trực tiếp từ Cloudflare trong update chính thức của incident (`Non-fedramp customers are not impacted`), không phải suy đoán từ tiêu đề.\n\n### 4. Giới hạn dữ liệu\n\n- Incident `wp49bp8bqo6c` vẫn **chưa resolved** và component vẫn `degraded_performance`, nhưng **không có update nào mới sau 2026-11-06T17:59Z** — tức đã ~3 ngày không có thông báo. Nếu phạm vi lỗi thay đổi, cần xem update mới trên status page.\n- Trạng thái tổng thể \"Partial System Outage\" có thể liên quan đến component khác ngoài cache purge — tra cứu chỉ theo từ khóa \"cache purge\" trong 50 incident gần nhất, nên không loại trừ tuyệt đối các sự cố khác.\n- Hai incident lịch sử chỉ có thể liên quan nếu site của bạn từng route qua Qingdao/EEUR đúng thời điểm đó — và chúng đã resolved nên không đáng kể hiện nay.\n- **Về nguồn URL:** URL của `wp49bp8bqo6c` có sẵn trong kết quả tool; URL của hai incident resolved (`5tlm9r9j9rpo`, `8s6pn9rvof3l`) do bạn cung cấp — kết quả tool cho hai incident này không chứa trường URL, nên tôi không thể xác minh chúng từ tool.\n\n**Khuyến nghị:** nếu CLB có nhu cầu purge gấp, có thể tham khảo workaround từng được Cloudflare nêu trong incident EEUR (Purge Everything, nhưng lưu ý tăng tải về origin). Nên theo dõi tiếp https://www.cloudflarestatus.com để cập nhật.
- Điểm ở lần chạy 1: 90/100

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```text
"trace": [
          {
            "event": "tool",
            "step": 1,
            "name": "get_status",
            "arguments": {
              "component": "cache purge",
              "provider": "cloudflare"
            },
            "result": {
              "provider": "cloudflare",
              "captured_at": "2026-11-09T03:50:47.638054+00:00",
              "overall": {
                "indicator": "major",
                "description": "Partial System Outage"
              },
              "components": [
                {
                  "name": "CDN Cache Purge",
                  "status": "degraded_performance"
                }
              ],
              "incidents": [
                {
                  "id": "wp49bp8bqo6c",
                  "name": "Fedramp Customers have Delayed Purges",
                  "status": "identified",
                  "updated_at": "2026-11-06T17:59:13.680Z"
                },
                {
                  "id": "5tlm9r9j9rpo",
                  "name": "Cache purge delays in Qingdao Shandong, China",
                  "status": "resolved",
                  "updated_at": "2026-10-31T06:57:35.247Z"
                },
                {
                  "id": "8s6pn9rvof3l",
                  "name": "Cache Purging Delays Affecting Requests Routed Through EEUR",
                  "status": "resolved",
                  "updated_at": "2026-10-27T16:14:34.056Z"
                }
              ],
              "matching_incidents_in_last_50": 3,
              "note": "Chỉ tìm theo chuỗi trong 50 incident gần nhất; không tìm thấy không chứng minh không có sự cố.",
              "source": "https://www.cloudflarestatus.com"
            }
          },
          {
            "event": "tool",
            "step": 2,
            "name": "get_incident_details",
            "arguments": {
              "incident_id": "wp49bp8bqo6c",
              "provider": "cloudflare"
            },
            "result": {
              "ok": true,
              "provider": "cloudflare",
              "captured_at": "2026-11-09T03:50:47.638054+00:00",
              "id": "wp49bp8bqo6c",
              "name": "Fedramp Customers have Delayed Purges",
              "status": "identified",
              "created_at": "2026-11-06T17:47:15.545Z",
              "updated_at": "2026-11-06T17:59:13.680Z",
              "components": [
                "CDN Cache Purge"
              ],
              "updates": [
                {
                  "status": "identified",
                  "created_at": "2026-11-06T17:59:13.680Z",
                  "body": "We are continuing to work on a fix for the issue.",
                  "truncated": false
                },
                {
                  "status": "investigating",
                  "created_at": "2026-11-06T17:47:15.545Z",
                  "body": "Cloudflare has identified a bug with our purge pipeline impacting fedramp customers. Stale content may be served until expired. Non-fedramp customers are not impacted. ",
                  "truncated": false
                }
              ],
              "updates_total": 2,
              "url": "https://www.cloudflarestatus.com/incidents/wp49bp8bqo6c"
            }
          },
          {
            "event": "tool",
            "step": 2,
            "name": "get_incident_details",
            "arguments": {
              "incident_id": "5tlm9r9j9rpo",
              "provider": "cloudflare"
            },
            "result": {
              "ok": true,
              "provider": "cloudflare",
              "captured_at": "2026-11-09T03:50:47.638054+00:00",
              "id": "5tlm9r9j9rpo",
              "name": "Cache purge delays in Qingdao Shandong, China",
              "status": "resolved",
              "created_at": "2026-10-31T06:41:15.562Z",
              "updated_at": "2026-10-31T06:57:35.247Z",
              "components": [
                "CDN Cache Purge"
              ],
              "updates": [
                {
                  "status": "resolved",
                  "created_at": "2026-10-31T06:57:35.247Z",
                  "body": "This incident has been resolved.",
                  "truncated": false
                },
                {
                  "status": "monitoring",
                  "created_at": "2026-10-31T06:50:05.303Z",
                  "body": "A fix has been implemented and we are monitoring the results.",
                  "truncated": false
                },
                {
                  "status": "investigating",
                  "created_at": "2026-10-31T06:41:15.562Z",
                  "body": "Cloudflare is observing cache purge delay in Qingdao in Shandong, China. \nWe are working to resolve this issue as soon as possible. More updates to follow.",
                  "truncated": false
                }
              ],
              "updates_total": 3,
              "url": "https://www.cloudflarestatus.com/incidents/5tlm9r9j9rpo"
            }
          },
          {
            "event": "tool",
            "step": 2,
            "name": "get_incident_details",
            "arguments": {
              "incident_id": "8s6pn9rvof3l",
              "provider": "cloudflare"
            },
            "result": {
              "ok": true,
              "provider": "cloudflare",
              "captured_at": "2026-11-09T03:50:47.638054+00:00",
              "id": "8s6pn9rvof3l",
              "name": "Cache Purging Delays Affecting Requests Routed Through EEUR",
              "status": "resolved",
              "created_at": "2026-10-27T15:10:35.555Z",
              "updated_at": "2026-10-27T16:14:34.056Z",
              "components": [
                "CDN Cache Purge",
                "Stockholm, Sweden - (ARN)"
              ],
              "updates": [
                {
                  "status": "resolved",
                  "created_at": "2026-10-27T16:14:34.056Z",
                  "body": "This incident has been resolved.",
                  "truncated": false
                },
                {
                  "status": "monitoring",
                  "created_at": "2026-10-27T16:02:33.460Z",
                  "body": "A fix has been implemented and we are monitoring the results.",
                  "truncated": false
                },
                {
                  "status": "investigating",
                  "created_at": "2026-10-27T15:10:35.555Z",
                  "body": "Some single-file cache purge requests (purge by URL) submitted through the Dashboard or API and routed through our EEUR purge queues are experiencing delays of up to 30 minutes. Cache invalidation for these requests may be delayed across our global network. This also affects Always Online.\nWe are working to resolve the issue. As a temporary workaround, you may use Purge Everything, but this may cause a surge in traffic to your origin server(s)\n\nMore updates to follow.",
                  "truncated": false
                }
              ],
              "updates_total": 3,
              "url": "https://www.cloudflarestatus.com/incidents/8s6pn9rvof3l"
            }
          },
          {
            "event": "validation_missing_source",
            "step": 3,
            "missing_urls": [
              "https://www.cloudflarestatus.com/incidents/5tlm9r9j9rpo",
              "https://www.cloudflarestatus.com/incidents/8s6pn9rvof3l"
            ]
          }
        ]
```

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu: 
    - Tập trung vào việc xử lý bằng chứng nguồn trong agent.py, đặc biệt là hướng dẫn agent sử dụng URL trả về từ tool get_incident_details.
    - Agent cần sử dụng đúng URL có trong tool result, không tự nhận định rằng dữ liệu thiếu URL hoặc gán nguồn cho người dùng khi không có căn cứ.
    - Evidence Gate kiểm tra URL đã quan sát được có xuất hiện trong câu trả lời hay không; nếu thiếu, ghi nhận validation_missing_source và yêu cầu agent bổ sung trong giới hạn số bước.
- Vì sao chọn thay đổi này:
    - E2 ở lần chạy đầu chỉ đạt 90/100. Judge phát hiện agent khẳng định sai rằng hai incident lịch sử không có trường URL, mặc dù URL thực tế đã xuất hiện trong tool result.
    - Đây là lỗi về tính chính xác của nguồn dẫn, không phải lỗi xác định incident. Vì vậy, tôi chọn cải thiện cách agent sử dụng và trình bày bằng chứng thay vì thay đổi toàn bộ quy trình ReAct.

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---:|---:|
| Điểm tổng | 96.64/100 | 96.95/100 |
| Điểm case E2 | 90/100 | 95/100 |
| Số model calls (agent + judge) | 85 (63 + 22) | 80 (58 + 22) |
| Tổng token (agent + judge) | 228,976 | 197,364 |
| Chi phí ghi nhận (agent + judge) | $0.05304 | $0.04516 |

*Chi phí và token được cộng từ trường `usage` của agent và `judge_usage` trong hai file JSON.*

- **Lỗi đã giải quyết:**
  - E2 không còn khẳng định sai rằng URL của các incident lịch sử bị thiếu trong tool result.
  - Câu trả lời sau đã dẫn đúng URL của incident đang hoạt động `wp49bp8bqo6c`, đồng thời trích dẫn chính xác thông tin khách hàng non-FedRAMP không bị ảnh hưởng.
  - Điểm E2 tăng từ 90 lên 95; số model calls của riêng E2 giảm từ 4 xuống 3.

- **Lỗi còn lại hoặc mới xuất hiện:**
  - H8 giảm từ 98 xuống 83. Judge phát hiện câu trả lời đã ghép trạng thái tổng thể Cloudflare thành một dòng incident không có trong tool result và tuyên bố quá mức rằng dữ liệu được liệt kê nguyên văn từ hai nhóm.
  - E2 vẫn bị trừ nhẹ vì phần khuyến nghị còn hơi dài.
  - Điểm tổng chỉ tăng 0.31 điểm, do đó chưa thể kết luận thay đổi đã cải thiện toàn bộ các trường hợp.

## 5. Giới hạn

- **Điểm judge:** Điểm số được đánh giá bởi LLM nên có thể chịu ảnh hưởng bởi cách diễn giải, mức độ nghiêm khắc và tính nhất quán của judge. Trong thí nghiệm này, agent và judge sử dụng cùng model `z-ai/glm-5.3-flash`, nên kết quả có thể có thiên lệch nhất định.
- **Snapshot dữ liệu:** Kết quả chỉ phản ánh snapshot tại `2026-11-09T03:50:47Z` từ các status page. Tool chỉ tìm kiếm trong phạm vi 50 incident gần nhất, vì vậy không thể xem đây là dữ liệu lịch sử đầy đủ hoặc trạng thái hiện tại ở thời điểm khác.
- **Số lần chạy:** Mỗi phiên bản chỉ được chạy một lần. Kết quả có thể thay đổi do tính không ổn định của LLM, nên mức tăng điểm chưa đủ để chứng minh thay đổi là nguyên nhân trực tiếp.
- **Giới hạn Evidence Gate:** Cơ chế kiểm tra URL chỉ xác nhận URL đã quan sát xuất hiện trong câu trả lời, không xác minh mọi nhận định trong câu trả lời đều được bằng chứng hỗ trợ. Trường hợp H8 cho thấy vẫn cần kiểm tra tính chính xác giữa từng incident và dữ liệu tool trả về.

## Thử thách bonus

### 1. Nội dung triển khai

Triển khai chức năng Bonus trong `agent.py`, đồng thời giữ nguyên giao diện hàm `run_agent()` và luồng xử lý của bài chính.

Các chức năng đã thực hiện:

- Hỗ trợ kiểm tra trạng thái của 5 nhà cung cấp: **GitHub, Cloudflare, Vercel, npm và DigitalOcean**.
- Sử dụng `tools.schemas` và `tools.system_prompt` khi được cung cấp bởi Bonus Tools.
- Xử lý lỗi có thể thử lại như HTTP 429 (Rate Limit), lỗi tham số không hợp lệ và timeout.
- Giới hạn số lần thử lại, hỗ trợ đọc giá trị `Retry-After` khi gặp HTTP 429.
- Lọc dữ liệu không liên quan trong kết quả từ tools nhằm giảm nhiễu ngữ cảnh.
- Hỗ trợ đọc timeline sự cố theo từng trang thông qua `offset`, `limit` và `next_offset`.
- Dẫn nguồn từ kết quả tools, phân biệt trạng thái snapshot hiện tại với sự cố trong lịch sử.
- Không suy diễn nguyên nhân khi chưa có đủ bằng chứng và không xem nội dung trả về từ tools là chỉ dẫn hệ thống.

### 2. Cách chạy đánh giá

Sử dụng lệnh:

```bash
python3 evals/run_evals.py --bonus --output eval_bonus.json
```

Cấu hình đánh giá:
- Provider: `openrouter`
- Model: `z-ai/glm-5.3-flash`
- Số bước tối đa: `16`
- Bộ kiểm thử: 10 trường hợp (B1–B10)

### 3. Kết quả đánh giá

| Tiêu chí | Kết quả |
|---|---:|
| Tổng số trường hợp kiểm thử | 10 |
| Số trường hợp được chấm điểm | 10 |
| Số trường hợp không được chấm | 0 |
| Điểm đánh giá | **98.9/100** |
| Điểm Bonus đạt được | **14.84/15** |
| Vi phạm cơ học | **0** |

Kết quả chi tiết được lưu trong file `eval_bonus.json`.
