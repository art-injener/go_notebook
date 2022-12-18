# Context

TODO: общее описание

### signal.NotifyContext: handling cancelation with Unix signals using context

Прерывание работы программы с использование context и Unix/Linux signals

ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt)
defer stop()


```go
func main() {
// Pass a context with a timeout to tell a blocking function that it
// should abandon its work after the timeout elapses.
ctx, stop := signal.NotifyContext(context.Background(), os.Interrupt)
defer stop()

select {
    case <-time.After(10 * time.Second):
        fmt.Println("missed signal")
    case <-ctx.Done():
        stop()
        fmt.Println("signal received")
}
}
```
