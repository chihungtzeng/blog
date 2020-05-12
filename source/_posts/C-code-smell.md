---
title: C++ code smell.
date: 2020-05-12 13:55:37
tags:
categories:
---

Source: https://www.youtube.com/watch?v=nqfgOCU_Do4

Highlight - Use sample code and ask audience how to refactor it.

- construction separate from assignment

```
std::string str;
str = "hello world";

vs

const std::string str = "hello world";
```

- out variables

```
void get_value(std::string& out);
int main()
{
  std::string value;
  get_value(value);
}

vs

std::string get_value();
int main()
{
  const auto value = get_value();
}

```

- Raw loop

```
void process_data(const std::vector<double>& values)
{
  bool in_range = true;
  for(const auto& v : values)
  {
    if (v<5.0 || v>100.0)
    {
      in_range = false;
      break;
    }
  }
  if (in_range)
  {
    process_more(values);
  }
}

Better:

void process_data(const std::vector<double>& values)
{
  const auto in_range = [](const double d) {return d>=5.0 && d<=100.0;};
  const bool all_in_rage = all_of(begin(values), end(values), in_range);
  if (all_in_range)
  {
    process_more(values);
  }
}
```

- static const is a code smell that should probably be `constexpr`.

static is initialized in thread-safe way. Need to lock/unlock and has performance hit.

- extern const

```
data.h:

extern int const value;

data.cpp:
#include "data.h"
int const value = 5;

int get_value()
{
  return value;
}
```

Better:

```
data.h:

constexpr int value = 5;
int get_value()
{
  return value;
}
```

- Raw `new` and `delete`

Better:

```
auto i = std::make_unique<int>(5);
```
