**수동으로 버튼을 눌러서 챗봇(Comment) URL로 이동하는 방식**으로, 다이어리를 조회할 때 **챗봇(Comment) URL**을 반환하며, 해당 URL은 프론트엔드에서 버튼으로 제공하도록 코드를 작성하게 되었다.


---

#### **1. `views.py`**
```python
from .models import Diary, Comment
from .serializers import DiarySerializer, CommentSerializer
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status

# 다이어리 상세 조회 (챗봇 URL 포함)
@api_view(['GET'])
def diary_detail(request, diary_id):
    try:
        # 다이어리 ID와 사용자 정보로 조회
        diary = Diary.objects.get(pk=diary_id, user=request.user)
    except Diary.DoesNotExist:
        return Response({"error": "다이어리를 찾을 수 없습니다."}, status=status.HTTP_404_NOT_FOUND)

    # 다이어리 직렬화
    diary_serializer = DiarySerializer(diary)

    # 다이어리와 연결된 Comment 검색
    try:
        comment = Comment.objects.get(diary=diary)
        comment_serializer = CommentSerializer(comment)  # Comment 직렬화
        chatbot_url = comment_serializer.data["chatbot_url"]
    except Comment.DoesNotExist:
        chatbot_url = None  # 댓글이 없으면 None 반환

    return Response(
        {
            "diary": diary_serializer.data,
            "chatbot_url": chatbot_url,  # 프론트에서 버튼을 렌더링할 때 사용
        },
        status=status.HTTP_200_OK,
    )

# 다이어리 생성
@api_view(['POST'])
def diary_create(request):
    serializer = DiarySerializer(data=request.data)
    if serializer.is_valid():
        # 다이어리 저장
        diary = serializer.save(user=request.user)

        # Comment 생성 및 챗봇 URL 저장
        chatbot_url = f"/chatbot/ai/comment?year={diary.select_date.year}&month={diary.select_date.month}&day={diary.select_date.day}"
        Comment.objects.create(diary=diary, chatbot_url=chatbot_url)

        return Response(serializer.data, status=status.HTTP_201_CREATED)

    return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

# AI 챗봇 URL에 해당하는 페이지
@api_view(['GET'])
def comment_link(request):
    year = request.GET.get('year')
    month = request.GET.get('month')
    day = request.GET.get('day')

    # AI 코멘트 관련 로직 추가 가능
    return Response(
        {"message": f"Redirected to AI chatbot comment page for {year}-{month}-{day}"}
    )
```

---

#### **2. `serializers.py`**
```python
from rest_framework import serializers
from .models import Diary, Comment

class DiarySerializer(serializers.ModelSerializer):
    user = serializers.StringRelatedField(read_only=True)

    class Meta:
        model = Diary
        fields = ['id', 'title', 'content', 'select_date', 'created_at', 'updated_at']
        read_only_fields = ['user', 'created_at', 'updated_at']

class CommentSerializer(serializers.ModelSerializer):
    chatbot_url = serializers.URLField()

    class Meta:
        model = Comment
        fields = ['id', 'chatbot_url']
```

---

#### **3. `urls.py`**
```python
from django.urls import path
from . import views

app_name = 'diaries'

urlpatterns = [
    path('<int:diary_id>/', views.diary_detail, name='diary-detail'),  # 다이어리 상세 조회
    path('diaries/create/', views.diary_create, name='diary-create'),  # 다이어리 생성
    path('chatbot/ai/comment/', views.comment_link, name='comment'),  # AI 코멘트 URL
]
```

---

### **작동 흐름**

1. **다이어리 상세 조회 API** (`/diary/<diary_id>/`)를 호출하면:
   - 다이어리 데이터와 함께 연결된 **챗봇(Comment) URL**을 반환한다.
   - 프론트엔드에서는 반환된 `chatbot_url` 값을 사용해 **"챗봇 이동" 버튼**을 렌더링한다.

2. **프론트엔드에서 버튼 클릭 시**:
   - 사용자는 `chatbot_url`로 이동하며, 해당 URL(`/chatbot/ai/comment`)에서 AI 코멘트를 확인하거나 대화할 수 있다.

3. **챗봇 URL 동작**:
   - `/chatbot/ai/comment` 페이지는 프론트엔드 혹은 별도의 API로 AI 코멘트를 처리하는 로직을 구현할 수 있다.

---

### 반환 예시

#### **다이어리 상세 조회 응답**
```json
{
    "diary": {
        "id": 1,
        "title": "오늘의 일기",
        "content": "오늘 날씨가 좋아서 기분이 좋았다.",
        "select_date": "2025-01-08",
        "created_at": "2025-01-08T12:34:56",
        "updated_at": "2025-01-08T12:34:56"
    },
    "chatbot_url": "/chatbot/ai/comment?year=2025&month=1&day=8"
}
```

프론트엔드에서 `chatbot_url`을 활용하여 사용자가 버튼을 클릭하면 챗봇 페이지로 이동하도록 구현하면 연결되지 않을까싶다.
사실 과제와 조금 어긋나긴하지만, 시간적 여유가 있을 때 고민해보았고, 추후 한번 작동되는지 찾아보려고한다. 