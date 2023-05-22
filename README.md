# ĐỀ TÀI : Phân tích bộ dữ liệu về các bộ phim khác nhau của ngành công nghiệp điện ảnh.

## Giới thiệu
 Dự án phân tích bộ dữ liệu về các bộ phim khác nhau của ngành công nghiệp điện ảnh là một dự án nhằm mục đích khám phá và tìm hiểu mô hình số liệu về các bộ phim từ các điểm như ngân sách, doanh thu, thể loại… Bộ dữ liệu được sử dụng trong dự án này được thu thập từ nhiều nguồn trên internet và điều chỉnh lại để đảm bảo tính đúng đắn và đầy đủ.

 Lý do cần thiết xây dựng dự án này là để tìm hiểu sâu hơn về ngành công nghiệp điện ảnh và các yếu tố ảnh hưởng tới thành công của một bộ phim. Dự án cũng có thể giúp cho các nhà sản xuất phim đưa ra những quyết định tốt hơn và phù hợp hơn trong quá trình sản xuất, đồng thời cũng cung cấp thông tin đến người dùng để giúp họ hiểu rõ hơn về các bộ phim mà họ yêu thích.
## Tổng quan
 Bộ dữ liệu phim là tập hợp dữ liệu về nhiều phim khác nhau, bao gồm các chi tiết như tiêu đề, thể loại, ngày phát hành, xếp hạng, v.v. Bộ dữ liệu này có thể được sử dụng cho nhiều mục đích phân tích dữ liệu và máy học khác nhau liên quan đến ngành công nghiệp điện ảnh.

 Mô tả tập dữ liệu: Tập dữ liệu phim bao gồm một tệp CSV duy nhất chứa thông tin về hơn 10.000 phim. Mỗi hàng trong tập dữ liệu đại diện cho một bộ phim và mỗi cột cung cấp thông tin chi tiết về bộ phim, chẳng hạn như:

    Tiêu đề: Tiêu đề của bộ phim.
    Năm phát hành: Năm bộ phim được phát hành.
    Thể loại: Thể loại phim.
    Thời gian chạy: Thời lượng của phim tính bằng phút.
    Đạo diễn: Tên đạo diễn của bộ phim.
    Nhà văn: Tên của nhà văn của bộ phim.
    Diễn viên: Tên các diễn viên xuất hiện trong phim.
    Rating: Đánh giá của bộ phim trên thang điểm từ 0 đến 10.
    Gross: Tổng doanh thu của bộ phim tính bằng đô la.
    Ngân sách: Ngân sách của bộ phim tính bằng đô la.
## Cách sử dụng: Bộ dữ liệu phim có thể được sử dụng cho nhiều mục đích khác nhau, chẳng hạn như:

 - Phân tích dữ liệu của ngành công nghiệp điện ảnh.
 - Mô hình dự đoán về doanh thu hoặc xếp hạng phim.
 - Hệ thống khuyến nghị cho phim.
 - Đào tạo các mô hình máy học liên quan đến dữ liệu phim. 

## ĐI VÀO PHÂN TÍCH
### I. Giới thiệu bộ dữ liệu

![1](https://drive.google.com/uc?id=1nXx8Aq75eleyjCcDFjBT4gT9np9psSZz)

### II. Xử lý và trực quan hóa dữ liệu
#### A. Xử lý dữ liệu
Kiểm tra các cột xem có cột nào có giá trị NAN không?
```py
df.isnull().sum()
```
![2](https://drive.google.com/uc?id=1rbrOoqWSrx3V6HA0WP60RKbPlrsmWqHP)

> => Có khá nhiều dữ liệu còn thiếu

So sánh giá trị NAN với giá trị có trong các cột.
```py
for i in df.columns:
    pct_missing = np.mean(df[i].isnull())
    print('{} - {}%'.format(i, pct_missing))
```

![3](https://drive.google.com/uc?id=1dkqa_0T3ercZRydaR7USGovTc39SA-Jx)
> => Số lượng dữ liệu có giá trị NaN không đáng kể với tổng giá trị các cột.

```py
# Cột budget
for i in df.index:
    if df.loc[i, 'budget'] == 0:
        df.loc[i, 'budget'] = pd.NaT
# # Cột gross
for i in df.index:
    if df.loc[i, 'gross'] == 0:
        df.loc[i, 'gross'] = pd.NaT
```
Sửa các giá trị bị thiếu trong các cột
```py
# Thay thê các giá trị trống bằng giá trị Median của mỗi cột
median_budget = df['budget'].median()
df['budget'].fillna(median_budget, inplace=True)
median_gross = df['gross'].median()
df['gross'].fillna(median_gross, inplace=True)
Sửa các giá trị có giá trị bằng 0 trong các cột thành giá trị NaT
```
Xóa các hàng khi cột name bị để trống
```py
df.dropna(subset=['name'], inplace=True)
```

Kiểm tra kiểu dữ liệu cho các cột
```py
df.dtypes
```
Thay đổi loại dữ liệu của cột budget và gross
```py
df['budget'] = df['budget'].astype('int64')
df['gross'] = df['gross'].astype('int64')
```
#### B. Trực quan hóa dữ liệu
10 đạo diễn hàng đầu với nhiều bộ phim phát hành  nhất
```py
director_counts = df['director'].value_counts().sort_values(ascending=False)
director_counts_df = director_counts.to_frame().reset_index()
director_counts_df.columns = ['director', 'count']
plt.figure(figsize=(10, 8))
sns.barplot(x='count', y='director', data=director_counts_df.head(10))
plt.title('10 đạo diễn hàng đầu với nhiều bộ phim phát hành  nhất')
plt.xlabel('Số Lượng phim')
plt.ylabel('Tên đạo diễn')
plt.show()
```

![13](https://drive.google.com/uc?id=1NLQjTN5sOVr3IDZ44_4EYyc1B-w8Goc6)

> - Biểu đồ cho ta biết 10 đạo diễn có nhiều sản phẩm nhất trong tập dữ liệu để giúp chúng ta có  thể giúp chúng ta có được cái nhìn toàn cảnh về sự phổ biến của các đạo diễn trong ngành công nghiệp phim. Biểu đồ này có thể giúp chúng ta xác định các đạo diễn có nhiều ảnh hưởng nhất và từ đó có thể đưa ra quyết định trong các kế hoạch sản xuất phim. 
> - Nó cũng có thể giúp chúng ta đánh giá hiệu quả trong việc sử dụng các đạo diễn khác nhau để sản xuất các loại phim khác nhau. Biểu đồ cũng có thể cung cấp thông tin về xu hướng và sở thích của thị trường đối tượng, giúp các nhà sản xuất phim hiểu rõ hơn về nhu cầu của khán giả và sản xuất các sản phẩm phim phù hợp.


Kinh phí trung bình của các bộ phim theo thể loại
```py
# # Tạo bảng dữ liệu mới chỉ chứa thông tin về kinh phí và thể loại của các bộ phim
budget_by_genre = df[['budget', 'genre']]
# # Tính kinh phí trung bình của các bộ phim theo thể loại
budget_by_genre = budget_by_genre.groupby('genre').mean()
# # Tạo biểu đồ cột thể hiện kinh phí trung bình của các bộ phim theo thể loại
fig = plt.figure()
ax = fig.add_axes([0.2, 0.3, 0.6, 0.6], facecolor='white')
ax.bar(budget_by_genre.index, budget_by_genre['budget'])
y_fmt = mtick.StrMethodFormatter('{x:,.0f}')
ax.yaxis.set_major_formatter(y_fmt)
ax.set_ylim([0, 10e7])
plt.xticks(rotation=90)
plt.xlabel('Thể loại phim')
plt.ylabel('Kinh phí trung bình')
plt.title('Kinh phí trung bình của các bộ phim theo thể loại')
plt.show()
```

![15](https://drive.google.com/uc?id=1ZIv4tZ1FIpjukGSl8QHMfa6gu5ROvx6K)


> - Đánh giá lợi ích của đầu tư: Biểu đồ này giúp các nhà sản xuất phim hiểu rõ hơn về chi phí trung bình đã đầu tư cho các bộ phim của từng thể loại, từ đó đưa ra quyết định về các ngân sách đầu tư phù hợp, giúp tối ưu hóa lợi nhuận.
> - Quản lý chi phí hiệu quả: Biểu đồ cung cấp cái nhìn tổng thể về chi phí đầu tư cho các bộ phim cùng thể loại giúp đa dạng hóa các kế hoạch đầu tư cho từng thể loại.
> - Xác định trọng tâm thị trường: Biểu đồ giúp đánh giá mức độ phát triển của các thể loại phim trong thị trường, từ đó ngành công nghiệp sẽ đưa ra kế hoạch phát triển những thể loại phim được ưa chuộng nhất trong thị trường


Doanh thu trung bình của các bộ phim theo thể loại
```py
# Tạo bảng dữ liệu  chứa thông tin về doanh thu và thể loại của các bộ phim
gross_by_genre = df[['gross', 'genre']]
# Tính doanh thu trung bình của các bộ phim theo thể loại
gross_by_genre = gross_by_genre.groupby('genre').mean()
# Tạo biểu đồ cột thể hiện doanh thu trung bình của các bộ phim theo thể loại
fig = plt.figure()
ax = fig.add_axes([0.2, 0.3, 0.6, 0.6], facecolor='white')
ax.bar(gross_by_genre.index, gross_by_genre['gross'])
y_fmt = mtick.StrMethodFormatter('{x:,.0f}')
ax.yaxis.set_major_formatter(y_fmt)
ax.set_ylim([0, 3e8])
plt.xticks(rotation=90)
plt.xlabel('Thể loại phim',fontsize=10)
plt.ylabel('Doanh thu trung bình')
plt.title('Doanh thu trung bình của các bộ phim theo thể loại')
plt.show()
```
![16](https://drive.google.com/uc?id=1FjkrUkk3VzUvxMW7jCItXHPz_QK8gKzG)

> - Biểu đồ này giúp các doanh nghiệp phân tích và đánh giá doanh thu trung bình của từng thể loại phim, từ đó họ có thể đưa ra các chiến lược kinh doanh phù hợp với từng đối tượng thị trường, tối ưu hóa doanh thu.
> - Phát triển kế hoạch tài chính: Biểu đồ cung cấp cái nhìn tổng quan về doanh thu trung bình của các bộ phim trong từng thể loại, giúp các nhà sản xuất phim và các doanh nghiệp đưa ra kế hoạch tài chính phù hợp, từ đó đảm bảo ngân sách hợp lý cho sản xuất phim.
> - Tổng kết kết quả công việc: Biểu đồ cho thấy kết quả kinh doanh trung bình của các bộ phim trong từng thể loại trong một khoảng thời gian, giúp nhà sản xuất phim đánh giá hiệu quả của công việc và đưa ra các chiến lược hoặc cải tiến để tăng doanh thu và lợi nhuận.
> - Dự báo tình hình kinh doanh: Biểu đồ là công cụ hữu ích để dự báo tình hình kinh doanh của các bộ phim trong tương lai và giúp các doanh nghiệp đưa ra chiến lược kinh doanh phù hợp với tình hình thị trường.

Tạo biểu đồ hiển thị top các loại xếp hạng (theo tên) mang lại doanh thu cao nhất.
```py
revenue_by_rating = df.groupby('rating')['gross'].sum().sort_values(ascending=False)
top_revenue_ratings = revenue_by_rating.head(10) # Lấy 10 loại xếp hạng có doanh thu cao nhất
plt.figure(figsize=(10, 8))
sns.barplot(x=top_revenue_ratings.index, y=top_revenue_ratings.values)
plt.title('Top các loại xếp hạng (theo tên) mang lại doanh thu cao nhất')
plt.xlabel('Xếp Hạng')
plt.ylabel('Doanh Thu')
plt.show
```

 ![17](https://drive.google.com/uc?id=1r3IBZkpqYXnvwBYjOVNQonCowyccncBx)


> - Biểu đồ giúp chúng ta có cái nhìn toàn cảnh về sự phổ biến và ảnh hưởng của các loại xếp hạng trong ngành sản xuất phim. Biểu đồ có thể giúp chúng ta tìm ra các xếp hạng phổ biến nhất và từ đó đưa ra các quyết định kinh doanh để tăng doanh thu.
> - Thông qua biểu đồ này, chúng ta cũng có thể phân tích và so sánh hiệu quả của các xếp hạng khác nhau. Chúng ta có thể tìm ra những xếp hạng nào mang lại doanh thu cao nhất và từ đó đưa ra một số kế hoạch, chiến lược nhằm tăng thêm doanh thu cho các chủ đề xếp hạng này.

Lợi nhuận phim thay đổi như thế nào trong nhiều năm?
```py
plt.figure(figsize=(20,10))
sns.lineplot(x='year', y='gross', data=df, ci=None)
plt.title("Lợi nhuận trung bình trong nhiều năm")
plt.xlabel("Năm")
plt.ylabel("Lợi nhuận")
plt.show()
```

![18](https://drive.google.com/uc?id=1iearChatYQOPwMvNrtHI6wpyGhR5QEnj)

> - Biểu đồ này sẽ cho thấy sự thay đổi của lợi nhuận phim qua nhiều năm và giúp người dùng dễ dàng nhận thấy xu hướng phát triển hoặc giảm sút của doanh thu qua thời gian.
> - Ý nghĩa của biểu đồ này là giúp các nhà sản xuất phim và các doanh nghiệp liên quan có cái nhìn tổng quan hơn về sự phát triển của ngành công nghiệp, giúp xác định xu hướng phát triển, cung cầu của thị trường, giúp các ngành doanh nghiệp có quyết định kinh doanh chính xác hơn.


Biểu đồ phân tán kinh phí và doanh thu của các loại  phim
```py
# Tạo bảng dữ liệu mới chỉ chứa thông tin về kinh phí và doanh thu của các bộ phim
budget_gross = df [['budget', 'gross', 'genre']]

#  Vẽ biểu đồ phân tán của kinh phí và doanh thu của các loại  phim

n_mau = len(budget_gross['genre'].unique())

cmap = cm.get_cmap('viridis')
values = np.linspace(0, 1, n_mau)
mau_hex = [cm.colors.to_hex(cmap(value)) for value in values]

for genre, color in zip(budget_gross['genre'].unique(),mau_hex):
    subset = budget_gross[budget_gross['genre'] == genre]
    plt.scatter(subset['budget'], subset['gross'], c=color, label=genre, alpha=0.6)
#
plt.xlabel('Kinh phí')
plt.ylabel('Doanh thu')
plt.title('Biểu đồ phân tán kinh phí và doanh thu của các loại  phim')
plt.legend(loc='upper right', ncol = 1,bbox_to_anchor =(1.13, 1.15))
plt.show()
```
![19](https://drive.google.com/uc?id=1POdNnhHglFwPLD-gtbs4O_lgVnKwyRIU)

> - Đánh giá mối quan hệ giữa kinh phí và doanh thu: Biểu đồ giúp các doanh nghiệp trong ngành công nghiệp sản xuất phim đánh giá mối quan hệ giữa kinh phí và doanh thu của các loại phim. Từ đó đưa ra quyết định tối ưu hóa chi phí sản xuất và cải thiện doanh thu.
> - Dự báo doanh thu: Biểu đồ giúp dự báo doanh thu của các bộ phim trong tương lai, đưa ra kế hoạch kinh doanh và ngân sách phù hợp với nhu cầu sản xuất phim.
> - Đánh giá hiệu quả kinh doanh: Biểu đồ cho thấy hiệu quả kinh doanh và lợi nhuận của các bộ phim trong từng loại. Từ đó, các doanh nghiệp có thể đưa ra các chiến lược sản xuất và quảng bá phim phù hợp với tình hình kinh doanh của mình.


Vẽ sơ đồ so sánh ngân sách so với doanh thu\
```py
budget_gross = df [['budget', 'gross', 'genre']]
plt.figure(figsize=(10, 8))
sns.set_style('darkgrid')
sns.regplot(x='budget', y='gross', data=budget_gross, color='#FF0000', scatter_kws={'alpha': 0.5},line_kws = {'color':'blue'})
plt.title('Ngân sách so với doanh thu')
plt.xlabel('Ngân Sách Thực Hiện')
plt.ylabel('Doanh Thu')
plt.show()
```

![20](https://drive.google.com/uc?id=1N_5HkuRNenU79iT6PQQhpUFK85AVoW0K)

> - Hiểu rõ hơn về mối quan hệ giữa ngân sách và doanh thu: Mô hình hồi quy tuyến tính giúp doanh nghiệp hiểu rõ hơn về mối quan hệ giữa ngân sách và doanh thu. Từ đó, họ có thể đưa ra các quyết định tối ưu hóa chi phí và tăng doanh thu.
> - Dự báo doanh thu: Mô hình hồi quy tuyến tính giúp dự báo doanh thu và đưa ra các kế hoạch kinh doanh phù hợp với mục tiêu tăng trưởng doanh thu.
> - Đánh giá hiệu quả kinh doanh: Mô hình hồi quy tuyến tính cho thấy hiệu quả kinh doanh và lợi nhuận của các bộ phim trong từng loại. 


Ma trận tương quan cho các cột dang số
```py
correlation_matrix = df.corr(method = 'pearson')
sns.heatmap(correlation_matrix, annot = True)
plt.title('Ma trận tương quan')
plt.xlabel('Các Cột Trong Data')
plt.ylabel('Các Cột Trong Data')
plt.show()
```
![21](https://drive.google.com/uc?id=1z7ti6QDTBAGpl30FkOViT759X5jC6Tkt)

>- Ma trận tương quan giúp phân tích mối quan hệ giữa các cột dữ liệu số, từ đó giúp cho việc xây dựng mô hình dự đoán chính xác hơn. Trong trường hợp này, doanh thu, kinh phí và thời gian phim có thể có mối tương quan với điểm đánh giá . Việc phân tích mối quan hệ giữa các cột giúp cho việc xác định mối quan hệ giữa các yếu tố ảnh hưởng đến sự thành công của mô hình đưa ra.
> - Ma trận tương quan cung cấp thông tin về tương quan giữa các cột dữ liệu số,giúp điều chỉnh số chiều dữ liệu và tối ưu hóa mô hình. 
