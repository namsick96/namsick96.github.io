---
title: "Directory"
excerpt: "OS"

categories:
    - OS
    - File System
tags:
    - OS
last_modified_at: 2021-06-02T21:06:00-10:00
sitemap :
  changefreq : daily
  priority : 1.0
--- 
# Directory란?
디렉토리란 유저에게 file을 organize한 구조를 보여줍니다. 디렉토리는 파일 시스템에게는 naming interface를 제공해 주는 존재입니다. 이러한 파일 시스템은 위계적인 구조를 가지고 다층적인 디렉토리 구조를 제공합니다.

## Directory의 구조
디렉토리도 일종의 file입니다. 근데 이제 metadata를 곁들인... 
아무튼 디렉토리는 metadata가 있는 파일이라고 보면 됩니다.
디렉토리에는 file 이름과 file attribute들이 존재합니다. 이게 메타데이터입니다. 이런 attribute에는 파일의 사이즈,제작일, access time, 디스크 내의 공간 등이 적혀있습니다.

이러한 디렉토리는 file을 빠르게 접근할 수 있게 해주고 (Efficiency)

유저가 이름을 편하게 기억하게 해주고 중복을 방지하며 (Naming)

file properties에따라 그룹핑 하게 해줍니다. (grouping)

이러한 디렉토리는 tree 구조로 저장되어 있어서 searching이 빠르고 grouping하기 쉽습니다.

유닉스에서는 이러한 디렉토리를 Acyclic-graph directories 형식으로 채택하고 있습니다. 이러한 acyclic-graph 형식은 서브디렉토리와 파일을 다른 디렉토리에서 같이 공유하는게 가능합니다. 그래서 한 디렉토리/파일을 삭제를 하면 dangling pointer 문제가 발생하는데 이걸 방지하기 위해서 graph를 타고 가서 연결된 다른 디렉토리에서도 해당 디렉토리/파일이 삭제 됨을 알려줍니다.

사용자가 파일에 접근하려면 그에 앞서서 파일 시스템이 mounting이 되어 있어야 시스템위에서 프로세스 위에서 사용될 수 있습니다. Unix 구조에서는 그냥 존재하는 빈 디렉토리(mount point)에 마운트를 합니다. 그러니깐 새로운 파일을 사용하려면 마운팅 포인트에 그냥 연결하면 되는겁니다.