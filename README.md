도식화 : https://excalidraw.com/#json=7r5xoPnDUHEqAsUHOD7VV,ND5ZO0PehD5rRCaN62HHVQ

S3 버킷 웹사이트 엔드포인트 : http://chapter4-1.s3-website-ap-southeast-2.amazonaws.com/

cloudfront : https://dug54vnnaytz4.cloudfront.net/

워크플로우 단계 요약
- Checkout → Install → Build → AWS 인증 → S3 업로드 → CloudFront 캐시 무효화

캐시무효화 시점:
- name: Invalidate CloudFront cache

npm run build로 정적파일 생성
aws s3 sync 로 정적 파일을 s3에 반영
그 직후 캐시 무효화

Cloudfront는 CDN이라 성능을 위해 이전 콘텐츠를 캐시해두는데 만약 캐시 무효화를 하지 않으면
사용자는 계속해서 이전 파일을 보게된다.

여기서 궁금했던게 캐시를 가져오는 것이 저는 효율적이라고 생각했는데 캐시를 계속사용하면 최신 파일을 가지고오지 못하는 단점이 있었습니다. 그렇다면 매번 캐시 무효화를 해야하는가?
그것은 아니었습니다. 캐시 무효화 + 캐시 사용은 적절히 이뤄져야 하는데 그 방법이 바로 파일명에 해시값 사용.

Next.js에서는 자동으로 적용되는데 다들 npm run build 해보셨을겁니다. 해보시면 고유빌드번호가 생성되고 각 파일별로 해시값이 붙은걸 확인할수 있습니다 (자세한건 도식화)
이때 해당 파일에 변화값이 생긴다면 다음 빌드때 해당 파일해시값은 변경되고 변화값이 없다면 해시값은 그대로 유지
즉, 해시값 변경 -> 변화값이 있는것으로 캐시 무효화 / 해시값 변경x -> 변화값이 없는 것으로 캐시 사용

### S3와 cloudfront 성능차이 살펴보기

https://velog.io/@jungtaesu/%EC%9D%B8%ED%94%84%EB%9D%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0