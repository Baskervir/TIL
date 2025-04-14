[리다이렉션 시 데이터 전달 방법]

1. RedirectAttributes.addAttribute()
    [설명]
        리다이렉션 URL에 쿼리 파라미터로 데이터를 추가한다.
    [특징]
        데이터가 URL에 노출되므로 민감한 정보에는 적합하지 않다.
        주로 간단한 데이터 전달에 사용된다.
    [예시]Java
        @PostMapping("/submit")
        public String submitForm(RedirectAttributes redirectAttributes) {
            redirectAttributes.addAttribute("status", "success");
            return "redirect:/result";
        }
            [위 코드는 /result?status=success로 리다이렉션된다.]

2. RedirectAttributes.addFlashAttribute()
    [설명]
        일회성 데이터를 세션에 저장하여 리다이렉션 후에도 데이터를 전달한다.
    [특징]
        데이터가 URL에 노출되지 않으므로 민감한 정보 전달에 적합하다. 
        데이터는 리다이렉션 후 첫 번째 요청에서만 유효하며, 이후에는 자동으로 제거된다.
    [예시]
        @PostMapping("/submit")
        public String submitForm(RedirectAttributes redirectAttributes) {
            redirectAttributes.addFlashAttribute("message", "Submission successful!");
            return "redirect:/result";
        }
            [리다이렉션된 /result 페이지에서는 message 속성을 사용할 수 있다.]

📌 주의사항
    addFlashAttribute()사용시 데이터가 세션에 저장되므로, 세션을 적절히 관리해야 한다.
    리다이렉션 후 첫 번째 요청에서만 플래시 속성이 유효하므로, 이후 요청에서는 해당 데이터에 접근할 수 없다.
    민감한 데이터는 addAttribute()를 사용하지 않고, 반드시 addFlashAttribute()를 사용하여 URL 노출을 방지해야 한다.
