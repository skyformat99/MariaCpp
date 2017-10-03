io<!-- -*- mode: markdown -*-  -->
http://mariacpp.roslaniec.net/     Thank You

MariaCpp is C++ library that lets you connect to the MariaDB Server  10.2.9
MariaDB Connector for/C  3.0.3  
LINUX not tested Windows

Major features of MariaCpp
--------------------------

*   __LGPL__ (with static link exception) license

*   thin C++ wrapper around _MariaDB Connector/C_ (C-API);
    _thin_ means that C++ objects have none (or minimal) internal state,
    and you can possibly mix C++ code with native C-API

*   works with C++98, C++11, C++14 standards

*   it takes benefits of 2 major C++ paradigms: __RAII__ and __exceptions__

*   supports most C-API features, including __prepared statements__

*   supports __multithreading__ (multiple connections to DB)

*   no other dependencies (e.g. no Boost dependency)



Example
-------


```C++
#include <mariacpp/lib.hpp>
#include <mariacpp/connection.hpp>
#include <mariacpp/exception.hpp>
#include <mariacpp/prepared_stmt.hpp>
#include <mariacpp/time.hpp>
#include <mariacpp/uri.hpp>
#include <cstdlib>
#include <iostream>
#include <memory>

using namespace MariaCpp;

const char *uri = "tcp://localhost:3306/test";
const char *user = "test";
const char *passwd = "";

int main()
{
    scoped_library_init maria_lib_init;

    try {
        Connection conn;
        conn.connect(Uri(uri), user, passwd);

        conn.query("CREATE TEMPORARY TABLE test "
                    "(i INT, s CHAR(15), d DATETIME)");
        std::auto_ptr<PreparedStatement> stmt(
            conn.prepare("INSERT INTO test (i,s,d) values(?,?,?)"));

        assert(3 == stmt->param_count());
        stmt->setInt(0, 1);
        stmt->setString(1, "string-1");
        stmt->setDateTime(2, Time("2016-03-23 02:41"));
        stmt->execute();

        stmt->setInt(0, 2);
        stmt->setNull(1);
        stmt->setDateTime(2, Time::datetime(2015, 02, 21, 12, 45, 51));
        stmt->execute();

        stmt.reset(conn.prepare("SELECT i, s, d FROM test ORDER BY i"));
        stmt->execute();
        while (stmt->fetch()) {
            std::cout << "i = " << stmt->getInt(0);
            std::cout << ", s = ";
            if (stmt->isNull(1)) std::cout << "NULL";
            else std::cout << stmt->getString(1);
            std::cout << ", d = " << stmt->getTime(2) ;
            std::cout << std::endl;
        }
        conn.close();
    } catch (Exception &e) {
        std::cerr << e << std::endl;
        return 1;
    }
    return 0;
}
```

    i = 1, s = string-1, d = 2016-03-23 02:41:00
    i = 2, s = NULL, d = 2015-02-21 12:45:51

FAQ
---

sources have been modified for compilation and add function...  
regard .... connection.hpp  { set_session start_transaction savepoint rollbackto release }
bind.cpp  lib.cpp   prepared_stmt.cpp

this is not valid for Mysql because compile and scheduled for MariaDB and test as well.
if not go back to the original site.

I thank for the work done by the author

-------------- Générer: Release in mariacpp (compiler: GNU GCC Compiler)---------------

g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/bind.cpp -o obj/bind.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/connection.cpp -o obj/connection.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/exception.cpp -o obj/exception.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/lib.cpp -o obj/lib.o
gcc -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/ma_time.c -o obj/ma_time.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/prepared_stmt.cpp -o obj/prepared_stmt.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/resultset.cpp -o obj/resultset.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/time.cpp -o obj/time.o
g++ -std=c++11 -Wall -O2 -I../../../../usr/include/mysql/ -I/home/soleil/CGIFCH/mariadb/ -c /home/soleil/CGIFCH/mariadb/uri.cpp -o obj/uri.o
rm -f bin/libmariacpp.a
ar -r -s bin/libmariacpp.a obj/bind.o obj/connection.o obj/exception.o obj/lib.o obj/ma_time.o obj/prepared_stmt.o obj/resultset.o obj/time.o obj/uri.o
ar: création de bin/libmariacpp.a
Output file is bin/libmariacpp.a with size 121,70 KB

* * *

**Q**: Why _MariaCpp_ is licensed as LGPL?

**A**: MariaCpp is licensed in the same spirit as _MariaDB Connector/C_.

* * *

