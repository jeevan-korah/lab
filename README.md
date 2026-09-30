#include <stdio.h>
#include <string.h>
char p[20][20],f[26][20],fo[26][20];
int n;
int add(char a[],char b) {
    int i;
    for(i=0;a[i];i++) {
        if(a[i]==b) return 0;
    }
    a[i]=b;
    a[i+1]='\0';
    return 1;
}
int copy(char a[],char b[]) {
    int i,c=0;
    for(i=0;b[i];i++) {
        if(b[i]!='#'&&add(a,b[i])) c=1;
    }
    return c;
}
void show(char name[],char a[][20]) {
    int i,j;
    printf("\n%s:\n",name);
    for(i=0;i<n;i++) {
        if(i==0||p[i][0]!=p[i-1][0]) {
            printf("%s(%c) = { ",name,p[i][0]);
            for(j=0;a[p[i][0]-'A'][j];j++) {
                printf("%c ",a[p[i][0]-'A'][j]);
            }
            printf("}\n");
        }
    }
}
int main() {
    int i,j,k,c,e;
    char l,s,x;
    printf("Enter number of productions: ");
    scanf("%d",&n);
    printf("Enter productions:\n");
    for(i=0;i<n;i++) {
        scanf("%s",p[i]);
    }
    do {
        c=0;
        for(i=0;i<n;i++) {
            l=p[i][0];
            for(j=2;p[i][j];j++) {
                s=p[i][j];
                if(s<'A'||s>'Z') {
                    if(add(f[l-'A'],s)) c=1;
                    break;
                }
                if(copy(f[l-'A'],f[s-'A'])) c=1;
                if(!strchr(f[s-'A'],'#')) break;
                if(!p[i][j+1]&&add(f[l-'A'],'#')) c=1;
            }
        }
    }while(c);
    add(fo[p[0][0]-'A'],'$');
    do {
        c=0;
        for(i=0;i<n;i++) {
            l=p[i][0];
            for(j=2;p[i][j];j++) {
                s=p[i][j];
                if(s<'A'||s>'Z') continue;
                e=1;
                for(k=j+1;p[i][k];k++) {
                    x=p[i][k];
                    if(x<'A'||x>'Z') {
                        if(add(fo[s-'A'],x)) c=1;
                        e=0;
                        break;
                    }
                    if(copy(fo[s-'A'],f[x-'A'])) c=1;
                    if(!strchr(f[x-'A'],'#')) {
                        e=0;
                        break;
                    }
                }
                if(e) {
                    if(copy(fo[s-'A'],fo[l-'A'])) c=1;
                }
            }
        }
    }while(c);
    show("FIRST",f);
    show("FOLLOW",fo);
    return 0;
}