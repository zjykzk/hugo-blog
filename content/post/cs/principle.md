+++
author = "zenk"
tags = ["method"]
draft = true
categories=["cs"]
title="编码的一些原则"
description="写代码中好用的一些原则。"
date="2018-10-19T11:08:05+08:00"

+++

## 测试

一个软件的功能需要正确，那么需要测试。对于软件的成功来说，自己测试是成本最低的。这里的成本公司层面，个人层面等等。所以，在软件开发的每个任务，都需要测试。比如，ut，压力，导流。

## mock结果

在写ut过程中经常需要mock接口。mock接口的返回结果是目前实现下来最简单的。以前，会根据参数mock返回结果，起始反而增加复杂度。

```
// Fooer the example interface
type Fooer interface {
    Foo() (int, error)
}

// tested func
func haliFoo(foo Fooer) error {
    c, err := foo.Foo()
    if err != nil {
        return err
    }
    // something others
    _ = c
    return nil
}

// doing testing
type mockFooer struct {
    fooErr error
    fooRet int
}

type (f *mockFooer) Foo() (int, error) {
    return f.fooRet, f.fooErr
}

func TestHaliFoo(t *testing.T) {
    mf := &mockFooer{}
    // mock Foo return error
    mf.fooErr = errors.New("bad fool")
    assert.Equal(t, mf.fooErr, haliFoo(mf))
}

```
