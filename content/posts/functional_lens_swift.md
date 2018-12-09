---
title: "Functional Lens in Swift"
date: 2018-12-09T21:02:35+06:30
draft: false
---

Swift Language က Lens အကြောင်း အခြေခံလေးပါ

ထားပါတော့ ကျွန်တော်တို့မှာ `Employee` ဆိုတဲ့ type ရှိတယ်ပေါ့။ ကျွန်တော်တို့က အဲ့ဒီ `Employee` ရဲ့ `Designation` တစ်ခုပဲပါတဲ့ View တစ်ခုဖန်တီးချင်တယ်ပေါ့။ အဲ့ဒီ `Designation` ကနေ `title` ဆိုတဲ့ value ကို get လုပ်ပြီးပြချင်တယ်ပေါ့။ နောက် project ရဲ့တစ်နေရာရာမှာ အဲ့ဒီ title ကို ပြင်ချင်တာမျိုးရှိလာနိုင်တယ် ပြီးတော့ အဲ့ဒီ `Employee` တစ်ခုလုံးကို update လုပ်တာမျိုးပေါ့။ အဲ့တော့ Code အနေနဲ့ကြည့်ရအောင်...

```
struct Employee {
    let name: String
    let designation: Designation
}

struct Designation {
    let title: String
    let department: String
}
```
အဲဒီမှာ Employee ရဲ့  title ကိုယူတာကတော့ရှင်းပါတယ်။ `employee.designation.title` ဆိုပြီးခေါ်လိုက်ရုံပဲ။ အဲ့ဒီ value ကို update လုပ်ဖို့ကျနည်းနည်းရှုပ်သွားပြီ။ ဘာလို့လဲဆိုတော့ ကျွန်တော်တို့က `Employee` နဲ့ `Designation` တို့ကို immutable structs အနေနဲ့ declare လုပ်ထားခဲ့လို့။ တစ်ကယ်လို့သာ mutable ဖြစ်ရင်တော့လွယ်လွယ် update လုပ်လိုက်လို့ရတာပေါ့။

အဲ့ဒီနေရာမှာ Lens funtion က အသုံးဝင်လာတာပါပဲ။ Lens ကို လွယ်လွယ်ပြောရရင်
getter နဲ့ setter ပေါင်းထားတာပါပဲ။ ကျွန်တော်တို့ ဥပမာကိုပဲ ပြောရရင် - `employee` ရဲ့ `designation` ကို ယူမယ် ပြီးရင် သူ့ `title` ကို update လုပ်ပြီး updated လုပ်ထားပြီးသား `Designation` နဲ့ `Employee` value အသစ် create လုပ်ပေးမယ်။ Code အနေနဲ့ကြည့်ရအောင် - 

```
struct Lens<A,B> {
	let from : (A) -> B
	let to : (B, A) -> A
}
```
ကျွန်တော်တို့ `Designation` ရဲ့ `title` ကို update လုပ်ဖို့ Lens function ကိုဒီလိုမျိုးရေးလို့ရမယ် - 
```
let title : Lens<Designation,String> = Lens(from: { $0.title }, to:{
	Designation(title: $0, department: $1.department)
})
```
ပြီးရင် title ကို update လုပ်ဖို့ ဒီလိုမျိုးသုံးလို့ရမယ် - 
```
let newDesignation = title.to("Mobile Developer", oldDesignation)
```
တစ်ကယ်လို့ Lens မသုံးခဲ့ဘူးဆိုရင် code ကဒီလိုမျိုးဖြစ်မယ် -
```
let newDesignation = Designation(title: "Mobile Developer", department: "Engineering")
```
ဒီတိုင်းဆို ဘာမှသိပ်မထူးခြားသေးသလိုပဲ။ အဲ့ဒီတော့ ကျွန်တော်တို့ composing lens တွေကို ဆက်ကြည့်ရအောင်။ ခုနက Designation ရဲ့ title ကို update လုပ်ဖို့ ရေးခဲ့တယ်၊ Employee ရဲ့ Designation ကို update လုပ်မယ်ဆိုလည်း တူတူပဲ။ ဒါပေမယ့် Employee ရဲ့ Designation ရဲ့ Title ကို update လုပ်မယ်ဆိုရင်ရော? Code အနေနဲ့ကြည့်ကြည့်ရအောင် - 
```
infix operator =>

func =><A,B,C>(l: Lens<A,B>, r: Lens<B,C>) -> Lens<A,C> {
    return Lens(from: { r.from(l.from($0)) }, to: { (c, a) in
        l.to(r.to(c,l.from(a)), a)
    })
}
```
ကျွန်တော်တို့ ဖတ်လို့ပိုကောင်းအောင် infix operator လေးနဲ့သုံးကြည့်မယ်

```
let employeeTitle = designation => title
```
Getter အနေနဲ့ဆိုရင် - 
```
let john = Employee(name: "John", designation: Designation(title: "Programmer", department: "Engineering"))
print(employeeTitle.from(john)) // Programmer
```
Update လုပ်မယ်ဆိုရင် - 
```
let john2 = employeeTitle.to("Mobile Engineer", john)
print(john2.designation.title) // Mobile Engineer
```