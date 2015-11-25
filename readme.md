Acid3
Acid3由网页标准计划小组（WebStandardsProject,WSP）设计，是一份网页浏览器及设计软件之标准相容性的测试网页，Acid3是目前Web标准基准测试中最严格的一个，对DOM和JavaScript有着严格的约束，Acid3于2008年3月3日正式发布。其测试焦点集中在ECMAScript、DOMLevel3、MediaQueries和data:URL。浏览器开启此测试网页后，页面会不断加载功能、直接给予分数，满分为100分。

不知道为什么没有在github上找到...
所以我copy了一份  https://github.com/lumixraku/Acid3


入口函数   update()
该函数是一个递归调用的函数   循环取出tests中的测试用例并执行
对于异步的测试用例  Acid没有采用任何异步处理方式  也没有任何流程库来控制

通过率
只要返回的不是retry都是通过
对于第一类测试用例  返回值都是1  第二类返回都是2
每次通过了  对应的类className 都加上一个p
所以这才是  zPPPPPPPPPPPPPPPP 这个类的由来



Acid总共分为6类测试

//第一类测试----------------------------------
DOM Traversal
// test 1: NodeFilters and Exceptions
不知道这个的实现为什么这么绕...
```javascript
    // test 1: NodeFilters and Exceptions
    var doc = getTestDocument(); // looks like <!DOCTYPE><html><head><title/><\head><body/><\html>     (the '\'s are to avoid validation errors)
    var iteration = 0;
    var exception = "Roses";
    var test = function(node) {
      iteration += 1;

      //凡是对于check中执行的nextNode()  都需要抛出指定的异常  也即是上面定义的对象exception
      switch (iteration) {
        case 1:
        case 3:
        case 4:
        case 6:
        case 7:
        case 8:
        case 9:
        case 14:
        case 15:
          throw exception;
        case 2:
        case 5:
        case 10:
        case 11:
        case 12:
        case 13:
          return true; // ToNumber(true) => 1
        default:
          throw 0;
      };
    };

    //非常严格的方式来检查method的存在和执行
    //只有对应的method存在 且执行后没有其他异常
    //那么就认为这个方法存在
    var check = function(o, method) {
      var ok = false;
      try {
        o[method]();
      } catch (e) {
        if (e === exception)
          ok = true;
      }
      assert(ok, "method " + o + "." + method + "() didn't forward exception");
    };
    var i = doc.createNodeIterator(doc.documentElement, 0xFFFFFFFF, test, true);
    check(i, "nextNode"); // 1   //若i.nextNode()存在  则调用i.nextNode()
    //由于这个test第一次执行  iteration是1  会抛出异常  所以这个nextNode()实际上没有执行成功

    //虽然这里是第二次执行nextNode()  但是由于第一次nextNode没有执行成功  这里才是第一次正确执行
    assertEquals(i.nextNode(), doc.documentElement, "i.nextNode() didn't return the right node"); // 2
    check(i, "previousNode"); // 3
    var w = document.createTreeWalker(doc.documentElement, 0xFFFFFFFF, test, true);
    check(w, "nextNode"); // 4
    assertEquals(w.nextNode(), doc.documentElement.firstChild, "w.nextNode() didn't return the right node"); // 5
    check(w, "previousNode"); // 6
    check(w, "firstChild"); // 7
    check(w, "lastChild"); // 8
    check(w, "nextSibling"); // 9
    assertEquals(iteration, 9, "iterations went wrong");
    assertEquals(w.previousSibling(), null, "w.previousSibling() didn't return the right node"); // doesn't call filter
    assertEquals(iteration, 9, "filter called incorrectly for previousSibling()");
    assertEquals(w.lastChild(), doc.getElementsByTagName('title')[0], "w.lastChild() didn't return the right node"); // 10
    assertEquals(w.nextSibling(), null, "w.nextSibling() didn't return the right node"); // 11 (filter called on parent, to see if it's included, otherwise it could skip that and find a nextsibling elsewhere)
    assertEquals(iteration, 11, "filter called incorrectly for nextSibling()");
    assertEquals(w.parentNode(), doc.documentElement.firstChild, "w.parentNode() didn't return the right node"); // 12
    assertEquals(w.nextSibling(), doc.documentElement.lastChild, "w.nextSibling() didn't return the right node"); // 13
    check(w, "previousSibling"); // 14
    check(w, "parentNode"); // 15
    return 1;
```



// test 2: Removing nodes during iteration
// test 3: the infinite iterator
// test 4: ignoring whitespace text nodes with node iterators
// test 5: ignoring whitespace text nodes with tree walkers
// test 6: walking outside a tree
// test 7: basic ranges tests
// test 8: moving boundary points
// test 9: extractContents() in a Document
// test 11: Ranges and Comments
// test 12: Ranges under mutations: insertion into text nodes
// test 13: Ranges under mutations: deletion


// test 14: HTTP - Content-Type: image/png
// test 15: HTTP - Content-Type: text/plain
// test 16: <object> handling and HTTP status codes ?

//  第二类测试 DOM2 --------------------------------------
// test 17: hasAttribute
// test 18: nodeType (this test also relies on accurate parsing of the document)
// test 20: nulls bytes in various places
// test 22: createElement() with invalid tag names
// test 23: createElementNS() with invalid tag names
// test 23: createElementNS() with invalid tag names
// test 24: event handler attributes
// test 25: test namespace checking in createDocumentType,
// test 26: check that document tree  create a document
// test 27: a continuation of the previous test (继续上次的测试  测试元素的 ?)
// test 28: getElementById()
// test 29: check that whitespace survives cloning  测试空白的节点复制?

// Events
// test 30: dispatchEvent()  自定义事件
// test 31: event.stopPropagation() and capture
// test 32: events bubbling through Document node //测试冒泡事件正确性



// bucket 3: DOM2 Views, DOM2 Style, and Selectors
(未完)



