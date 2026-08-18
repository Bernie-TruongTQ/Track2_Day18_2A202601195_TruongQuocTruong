## Anti-pattern dễ mắc nhất: Small files và thiếu maintenance

Trong Top 5 Lakehouse Anti-Patterns, team tôi dễ vướng nhất là để streaming ingestion tạo quá nhiều file nhỏ rồi coi đó là bình thường. Mỗi micro-batch chỉ vài trăm hoặc vài nghìn dòng nên việc ghi nhanh và đơn giản, nhưng chi phí bị dồn sang truy vấn, lập kế hoạch và metadata.

Notebook 06 cho thấy 200 commit tạo 200 file cho 100.000 dòng. Compaction giảm còn 11 file, tức ít hơn 18 lần; clustering sau đó bỏ qua 90% file cho một point query. Nếu không có bảo trì, số file tăng theo thời gian, làm cold start chậm, tăng request object storage và phình metadata. Vacuum cũng không tự phát hiện file mồ côi do job lỗi.

Nguyên nhân là team tối ưu throughput của writer mà quên chi phí đọc. Chúng tôi sẽ đặt micro-batch hợp lý, giới hạn file mỗi partition, theo dõi file-count và lập lịch compaction, clustering, checkpoint, snapshot expiry và orphan sweep. Mỗi job phải có metric trước/sau và age guard.
