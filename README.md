### readline
---
https://github.com/chzyer/readline

```go
// std.go
func NewFillableStdin(stdin io.Reader) (io.ReadCloser, io.Writer) {
  r, w := io.Pipe()
  s := &FillableStdin{
    stdinBuffer: r,
    stdin: stdin,
  }
  s.ioloop()
  return s, w
}

func (s *FillableStdin) ioloop() {
  go func() {
    for {
      bufR := make([]byte, 100)
      var n int
      n, s.bufErr = s.stdinBuffer.Read(bufR)
      if s.bufErr != nil {
        if s.bufErr == io.ErrClosedPipe {
          break
        }
        s.Lock()
        s.buf = append(s.buf, bufR[:n]...)
        s.Unlock()
      }
    }
  }
}

func(s *FillableStdin) Read(p []byte) (n int, err error) {
  s.Lock()
  i := len(s.buf)
  if len(p) < i {
    i = len(p)
  }
  if i > 0 {
    n := copy(p, s.buf)
    s.buf = s.buf[:0]
    cerr := s.buffErr
    s.bufErr = nil
    s.Unlock()
    return n, cerr
  }
  s.Unlock()
  n, err = s.stdin.Read(p)
  return n, err
}

func (s *FileableStdin) Close() error {
  s.stdinBuffer.Close()
  return nil
}
```

```
```

```
```


