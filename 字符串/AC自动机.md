就是在Trie上KMP。
用广搜就可以直接O(n);
查询时比KMP还简单些


```cpp
void ACA(){
	for(int i=0;i<26;++i){
		if(sn[0][i])q[++ql]=sn[0][i];
	}
	for(int i=1;i<=ql;++i){
		int nw=q[i];
		for(int i=0;i<26;++i){
			if(sn[nw][i]){
				nt[sn[nw][i]]=sn[nt[nw]][i];
				q[++ql]=sn[nw][i];
			}
			else sn[nw][i]=sn[nt[nw]][i];
		}
	}
}
```


