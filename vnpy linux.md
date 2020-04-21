1. clone source

```bash
git clone https://vnpy/vnpy.git
cd vnpy
```

2. create venv

```bash
python3 -m venv venv
source venv/bin/active
```

3. install ta-lib

```bash
wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
tar -xzf ta-lib-0.4.0-src.tar.gz
cd ta-lib
./configure --prefix=/usr
make
sudo make install
```

4. build & install vnpy

```bash
python setup.py install
```

5. run

create file run.py (same example/vn_trader/run.py but comment some codes)

```python
# flake8: noqa
from vnpy.event import EventEngine

from vnpy.trader.engine import MainEngine
from vnpy.trader.ui import MainWindow, create_qapp
from vnpy.app.portfolio_strategy import PortfolioStrategyApp


def main():
    """"""
    qapp = create_qapp()

    event_engine = EventEngine()

    main_engine = MainEngine(event_engine)
    main_engine.add_app(PortfolioStrategyApp)
    
    main_window = MainWindow(main_engine, event_engine)
    main_window.showMaximized()

    qapp.exec()


if __name__ == "__main__":
    main()

```

```bash
python run.py
```

**NOTE: must run in Linux with GUI**
