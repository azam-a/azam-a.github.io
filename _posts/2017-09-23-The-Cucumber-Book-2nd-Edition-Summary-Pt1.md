---
layout: post
title: The Cucumber Book (2nd Edition) Summary - Part 1 - Fundamentals
image: /images/cucumber_review/book_cover.png
---

![Cucumber Book Second Edition Cover]({{ site.baseurl }}/images/cucumber_summary/book_cover.png){:height="500px"}
 
tl;dr: Read the book, it's that good !

<!--more-->

Recently I started to read the book, which was written by the creator of Cucumber themselves. The book walks the reader through Cucumber features, and some guidance on how to effectively use Cucumber.

It also has examples on how to use Cucumber in different types of applications - CLI, REST Web Service, Ruby on Rails, and AJAX Web applications. The book also helps to identify situations on how to spot when Cucumbers go bad, which I will cover in Part 2 of the summary. In Part 1, I will focus on Cucumber fundamentals.

Here are a few TILs from the book:

- `Step Definition` will match anything no matter if it is defined as `Given()`, `When()`, or `Then()`. For example, a step defined as `Given()`, will also match a a step called from a `Scenario` as `When` or `Then`, as long as the regular expression matches. The `Given()`, `When()`, and `Then()` are just aliases to make things more readable and organisable. That also explains why `And` and `But` can be used to match any `Step Definition` without the need to implement `And()` or `But()`.

- Related to the preceding item, it is possible to replace `Given`, `When`, `Then`, `But` and `And` in `Feature` file with asterisks `*`. However the readability is debatable and it depends on the team whether it makes sense to the more verbose standard keywords or asterisks:

{% highlight cucumber %}
Scenario: Sign up for new account with existing email
  * Account with email "myemail" already exists
  * I sign up for new account using "myemail"
  * I should see a message saying the account already exists
{% endhighlight %}


- All steps will be treated as passed by default, unless an exception is returned.

- `Feature` keyword is just for documentation purposes. It can support multine description, or even empty.

- `Data Table` can be used to describe information not fit on a single line of step. So no more multiple steps to describe a complex `Scenario`. `Data Table` can be accessed using an array-like structure within the steps. Example:

{% highlight cucumber %}
Scenario: List existing items
  Given these Items exist:
    | name        | quantity |
    | USB Hub     |    5     |
    | HDMI Cable  |    7     |
    | LCD Monitor |    3     |
{% endhighlight %}

{% highlight ruby %}
  Given(/^these Items exist:$/) do |table|
    # table is a Cucumber::MultilineArgument::DataTable
    @items = table.raw
    puts @items
  end
{% endhighlight %}

{% highlight ruby %}
  [["name", "quantity"], ["USB Hub", "5"], ["HDMI Cable", "7"], ["LCD Monitor", "3"]]
{% endhighlight %}

- `Scenario Outline` supports multiple `Examples` tables, each with own description. This feature enables categorising of examples, like Positive case, Exceptional case, etc. and at the same time sharing the same `Scenario Outline`. Example:

{% highlight cucumber %}
Feature: Shipping charge calculation
  
  Scenario Outline: Calculate shipping charge
    Given the package type is <Type>
    And destination is <Destination>
    Then I should see that the shipping charge is <Charge>
      
      Examples: Letters
        Letters are subject to flat rate shipping

        | Type   | Destination   | Charge |
        | letter | domestic      | MYR 15 |
        | letter | international | MYR 15 |

      Examples: Small Packet
        Small packet for international has additional export charge

        | Type         | Destination     | Charge |
        | small packet | domestic        | MYR 50 |
        | small packet | international   | MYR 75 |
{% endhighlight %}

- `Nested Steps` are allowed in Cucumber. That means a `Step Definition` can call another `Step Definition`. However it is discouraged, as most of the time, it is easier to implement certain logic in the `Step Definition` itself instead of abstracting it further through `Nested Steps`. [Reference](https://github.com/cucumber/cucumber/wiki/Calling-Steps-from-Step-Definitions)

- `Doc Strings` allow multiline data to be attached to a preceding step. This allows some snippets like JSON, XML, HTML, etc. to be specified and attached inline in a `Scenario`. Example:

{% highlight cucumber %}
  
Scenario: Suspend abusive user
  When I post the same external URL 5 times in 1 minute
  Then my account should get suspended
  And I should receive an email:
    """
    Dear User,

    Your account has been automatically suspended by our system
    due to your posting pattern.

    If you think that the system had mistakenly flagged your account,
    please send an email to abuse@random56453.com
    """
{% endhighlight %}

- `Tags` can be used at `Feature`, `Scenario`, `Scenario Outline` and `Examples` levels. Tags can be inherited if they are defined at higher levels. Multiple tags can be specified using spaces (`@slow @endtoend`). Tags are useful because they can be used for documentation, filtering for running or reporting, and also for before/after `hooks` that can target specific tags.

- `Background` should be used to set up complex conditions, so that the `Scenario` only contain its essence and intent, without the noise.

- Split `Scenario` if it is too long. Likewise, split `Feature` if it is too complex.

- `Scenario` name should focus more on the `Given` and `When`, not so much on `Then`

- Beware of `Step` phrasing, as one ambiguous step intended for `putting the data`, `checking the data`, or `modifying the data` might be mistakenly reused to compose another Scenario that may result in invalid outcome. For example, a step called `Given the account balance is $50` could be implemented to set up the account balance, however someone could mistekenly reuse it to check the account balance in a `Then` step: `Then the account balance is $50`. Then the result will be wrong as the step will put the data instead of checking the data, and the step will always pass.


I think that's it for Part 1. Part 2 will cover how to spot when Cucumbers go bad, the symptoms, the causes and the remedy.