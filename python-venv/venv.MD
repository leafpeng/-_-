> Create env
```
python -m venv venv
```
> Activate env-windows
```
venv\Scripts\activate.bat
```
> Activate env-linux
```
source venv/bin/activate
```
> Deactivate env
```
deactivate
```
> Requirements.txt
```
pip freeze
```
> Install from re.txt
```
pip install -r requirements.txt
```
> Create env with system packages
```
python -m venv venv --system-site-packages
```
> List local env packages
```
pip list --local
pip freeze --local
```