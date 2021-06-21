---
title: "Shell | 差异备份"
tags: ["shell"]
categories: ["脚本"]
date: 2021-06-06T21:10:00+08:00
toc: false
draft: false
---

```shell
#!/bin/bash
# name:diff_backup.sh
BASE_DIR=/tmp/backup
SRC_DIR=$BASE_DIR/source
BAK_DIR=$BASE_DIR/backup
FULL_DIR=$BAK_DIR/full
UP_DIR=$BAK_DIR/update
CHECK_DIR=$BAK_DIR/check
TODAY=$(date +%H_%M_%S)
F_LIST=$BASE_DIR/file.list
T_KEY=$BASE_DIR/time.key
F_EMP=/tmp/$(basename $0_empty)
 
full()
{
    touch $T_KEY
    cd $SRC_DIR
    [ $? -ne 0 ] && echo "wrong" && exit 1
    mkdir -p $FULL_DIR/$TODAY
    find ./ ! -type d > $F_LIST
    tar cf - -T $F_LIST | tar xf - -C $FULL_DIR/$TODAY
    sed -i "s%$% $FULL_DIR/$TODAY%" $F_LIST
}
 
update()
{
    [ ! -f $T_KEY ] && full
    touch $T_KEY.tmp
    cd $SRC_DIR
    [ $? -ne 0 ] && echo "wrong" && exit 1
    mkdir -p $UP_DIR/$TODAY
    find ./ ! -type d -newer $T_KEY > $F_LIST.update
    tar cf - -T $F_LIST.update | tar xf - -C $UP_DIR/$TODAY
    while read F_NAME
    do
        STMP=$(grep "$F_NAME" $F_LIST)
        if [ -z "$STMP" ]
        then
            echo "$F_NAME $UP_DIR/$TODAY" >> $F_LIST
        else
            sed -i "s%$STMP%$F_NAME $UP_DIR/$TODAY%" $F_LIST
        fi
    done < $F_LIST.update
    /bin/mv $T_KEY.tmp $T_KEY
}
 
check()
{
    > $F_EMP
    [ ! -f $T_KEY ] && full
    touch $T_KEY.tmp
    cd $SRC_DIR
    [ $? -ne 0 ] && echo "wrong" && exit 1
    mkdir -p $UP_DIR/$TODAY
    find ./ ! -type d -newer $T_KEY > $F_LIST.update
    tar cf - -T $F_LIST.update | tar xf - -C $UP_DIR/$TODAY
    while read F_NAME
    do
        STMP=$(grep "$F_NAME" $F_LIST)
        if [ -z "$STMP" ]
        then
            mkdir -p $(dirname $CHECK_DIR/$TODAY/$F_NAME)
            diff $F_EMP $UP_DIR/$TODAY/$F_NAME > $CHECK_DIR/$TODAY/$F_NAME
            echo "$F_NAME $UP_DIR/$TODAY" >> $F_LIST
        else
            mkdir -p $(dirname $CHECK_DIR/$TODAY/$F_NAME)
            diff $(echo $STMP | awk '{ print $2}')/$F_NAME $UP_DIR/$TODAY/$F_NAME > $CHECK_DIR/$TODAY/$F_NAME
            sed -i "s%$STMP%$F_NAME $UP_DIR/$TODAY%" $F_LIST
        fi
    done < $F_LIST.update
    /bin/mv $T_KEY.tmp $T_KEY
}
 
case $1 in
    full)
        full
    ;;
    update)
        update
    ;;
    check)
        check
    ;;
    *)
        echo "usage $0 [full|update|check]"
esac
exit 0
```


