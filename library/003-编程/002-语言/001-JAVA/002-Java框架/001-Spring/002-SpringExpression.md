
[说说 Spring 表达式语言（SpEL）的核心类与用法](https://blog.csdn.net/deniro_li/article/details/82527710)

Demo

```java
package com.spring.dev.expression;

import lombok.AllArgsConstructor;
import lombok.Data;
import org.springframework.expression.EvaluationContext;
import org.springframework.expression.Expression;
import org.springframework.expression.ExpressionParser;
import org.springframework.expression.spel.standard.SpelExpressionParser;
import org.springframework.expression.spel.support.StandardEvaluationContext;

import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author hyzhangj
 */
public class SpringExpressionLanguageTest {
    private static ExpressionParser parser = new SpelExpressionParser();

    public static void main(String[] args) {
        Para para = new Para(new Task("12345", "TestAbc", Arrays.asList("Svr1", "Svr2")));

        EvaluationContext context = new StandardEvaluationContext(para);

        System.out.println(parser.parseExpression("task.taskId").getValue(context, String.class));
        System.out.println(parser.parseExpression("task.services[1]").getValue(context, String.class));
        System.out.println(parser.parseExpression("task.taskId == '12345'").getValue(context, Boolean.class));
        System.out.println(parser.parseExpression("task.taskId.equals('12345')").getValue(context, Boolean.class));
    }
}

@Data
@AllArgsConstructor
class Para {
    private Task task;
}

@Data
@AllArgsConstructor
class Task {
    private String taskId;
    private String taskName;
    private List<String> services;
}


------------------------------------------------------
12345
Svr2
true
true

```