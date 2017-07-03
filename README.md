# SNR_code
#Still working out the problems in this code
import math
from pycbc import types
from pycbc import fft
from pycbc import waveform
from pycbc.waveform import get_td_waveform
from pycbc.filter import match
import pycbc.psd.read
from pycbc.waveform import get_fd_waveform
from pycbc.filter.matchedfilter import make_frequency_series
from pycbc.filter import highpass_fir, matched_filter

f_low = 40.00
sample_rate = 4096

sp,sc = waveform.get_fd_waveform(approximant = "TaylorF2",
                                 mass1= 1.4,
                                 mass2= 1.4,
                                 delta_f= 1.0/sample_rate,
                                 f_lower= f_low)

hp, hc = waveform.get_td_waveform(approximant = "TaylorT4",
                                  mass1 = 1.4,
                                  mass2 = 1.4,
                                  delta_t = 1.0/sample_rate,
                                  f_lower = f_low)
A = pycbc.psd.read.from_txt("aLIGO", 2999, 0.003,1.003,is_asd_file = False)
hp.resize(len(sp)/2+1)
snr = matched_filter(hp,sp, psd= A, low_frequency_cutoff = 20.0)
snr = snr[len(snr)/4: len(snr)*3/4]
import pylab
pylab.plot (snr.sample_times, abs(snr))
pylab.ylabel('signal-to-noise')
pylab.xlabel('GPS Time (s)')
pylab.show()
